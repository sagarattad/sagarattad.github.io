---
title: "Digital Signatures in SAP Adobe Forms: Setup, Testing and ABAP"
description: "How to put a server-side digital signature on an Adobe form: what BASIS needs to set up, how to test it with FP_PDF_TEST_07, the two errors you'll probably hit, and the ABAP that does the signing."
categories: [ABAP, SAP]
---

Sooner or later, someone asks for an SAP output to carry a *digital* signature, not just a scanned image of one. Invoices, certificates of analysis and compliance documents are common examples. In SAP Interactive Forms by Adobe this is very doable, but it touches several teams: business, BASIS and ABAP. This post walks through the whole path, from what a digital signature actually is to the ABAP that applies one.

## What is a digital signature?

A digital signature is a cryptographic seal on a document. The signer uses a private key to sign it. Anyone can then check, with the matching public certificate, two things: *who* signed it, and that *nothing has changed* since.

It's not a picture of a handwritten signature pasted onto a PDF. It's math, which is why it can't be copied from one document onto another or quietly edited afterwards.

It gives you three guarantees:

| Purpose | What it means |
|---|---|
| **Authentication** | The receiver can trust that the document really came from the stated sender. |
| **Integrity** | The document wasn't altered after it was signed. Change one character and the signature breaks. |
| **Non-repudiation** | The signer can't later deny having signed it. |

That's why digital signatures show up wherever documents carry legal or financial weight: government, manufacturing, healthcare, financial services and cryptocurrencies.

## Two ways to sign Adobe forms in SAP

**Server-side signature.** Adobe Document Services (ADS) applies the signature on the server while it renders the PDF. The form needs a *signature field*, which is where the signature is stored. This is the approach the rest of this post covers.

**Qualified digital signature.** Some countries, such as Germany and Austria, have legal rules for "qualified" signatures. In simple terms, the signing certificate must live on separate hardware. On the client side, vendor-specific Adobe Reader plug-ins can apply them. ADS **cannot** apply qualified signatures on the server, and how they're validated depends on the vendor.

## Prerequisites

Most of the setup isn't ABAP at all. Get these in place before writing any code:

| # | Step | Who |
|---|---|---|
| 1 | Set up an **SSL connection to ADS** | BASIS |
| 2 | Enable **interactive functionality** for forms | BASIS |
| 3 | Configure SSL with **certificate-based** or **basic authentication** | BASIS |
| 4 | Get a **digital signature certificate** from a Certificate Authority (CA) | Business |
| 5 | **Upload the certificate** to the ADS system, under an alias (the "key") | BASIS, with the certificate from business |

> Ask BASIS for two names once they're done: the **ADS SSL connection** (for example `ADS_SSL`) and the **certificate alias** they used during the upload. You need both for testing and in your print program.
{: .callout .tip}

## Test it before you write any code

SAP ships a test report, **`FP_PDF_TEST_07`** ("Test Program: Server-Side Signature"). It takes an existing PDF, signs it through ADS and saves the result. If this works, your whole setup works, and any remaining problems are in your own code.

First, create a PDF from your form with an empty signature field. Then run the report with:

| Field | What to enter |
|---|---|
| **PDF File Name** | The input PDF, with the blank signature field |
| **PDF Result** | Where to save the signed output PDF |
| **Connection** | The ADS SSL connection name, e.g. `ADS_SSL` |
| **Key** | The certificate alias BASIS gave the certificate when uploading it to ADS, e.g. `ACME_CORP` |
| **Field Name** | The signature field's name in your Adobe form, e.g. `SignatureField1` |

<figure>
  <img src="{{ '/assets/images/posts/digital-signature/test-report.png' | relative_url }}" alt="FP_PDF_TEST_07 selection screen with input and output PDF paths, the ADS_SSL connection, key ACME_CORP and field name SignatureField1">
  <figcaption>FP_PDF_TEST_07 with sample values. The key is the certificate alias in ADS.</figcaption>
</figure>

Reason, location and contact info are optional texts that get stored with the signature.

Open the input PDF and you'll see an empty signature box:

<figure>
  <img src="{{ '/assets/images/posts/digital-signature/unsigned-pdf.png' | relative_url }}" alt="Input PDF with an empty signature field">
  <figcaption>Before: the input PDF with an empty signature field.</figcaption>
</figure>

Open the output in Adobe Reader and the box now holds the signer's name and certificate details. The blue bar at the top reads *"Signed and all signatures are valid."*

<figure>
  <img src="{{ '/assets/images/posts/digital-signature/signed-pdf.png' | relative_url }}" alt="Output PDF in Adobe Reader showing 'Signed and all signatures are valid' and the signature details of DS ACME CORP INDIA PRIVATE LIMITED 1">
  <figcaption>After: the signed output, validated by Adobe Reader. (Sample organization.)</figcaption>
</figure>

## The two errors you'll probably hit

**`201.701` — No SSL connection for operation SET_SIGNATURE**

<figure>
  <img src="{{ '/assets/images/posts/digital-signature/error-201701.png' | relative_url }}" alt="Usage error 201.701: No SSL connection for operation SET_SIGNATURE">
</figure>

ADS only signs over a secure connection. Either the SSL connection to ADS hasn't been set up yet, or you passed the normal (non-SSL) ADS connection in the **Connection** field.

**`201.501` — Sign requires a secure connection**

<figure>
  <img src="{{ '/assets/images/posts/digital-signature/error-201501.png' | relative_url }}" alt="System error 201.501: Sign requires a secure connection. Please install the high encryption PDF Manipulation service and enable SSL.">
  <figcaption>"Sign requires a secure connection. Please install the high encryption PDF Manipulation service and enable SSL."</figcaption>
</figure>

The connection exists, but high-encryption SSL isn't configured on the ADS side. BASIS needs to finish the SSL setup with certificate-based or basic authentication. See SAP Note 697327.

## The ABAP: signing a PDF

Use `FP_PDF_TEST_07` as your reference. The core of the signing logic is short: create a PDF object on the SSL connection, give it the rendered PDF, tell it which certificate and field to use, and let ADS do the work.

```abap
TRY.
    " create PDF object on the ADS SSL connection
    l_pdfobj = l_fp->create_pdf_object( connection = p_dest ).

    " set the document to be signed
    l_pdfobj->set_document( pdfdata = l_pdf ).

    " set the signature
    l_pdfobj->set_signature(
      keyname     = s_key       " certificate alias in ADS
      fieldname   = s_field     " signature field in the form
      reason      = s_reason
      location    = s_loc
      contactinfo = s_cinfo ).

    " execute: call ADS
    l_pdfobj->execute( ).

    " get the signed result
    l_pdfobj->get_document( IMPORTING pdfdata = l_out ).
  CATCH cx_fp_runtime INTO DATA(lx_fp).
    " handle and log the error
ENDTRY.
```

- `L_PDF` is the `XSTRING` of the rendered form that still needs the signature.
- `L_OUT` is the `XSTRING` of the signed form.

<figure>
  <img src="{{ '/assets/images/posts/digital-signature/abap-code.png' | relative_url }}" alt="The signing section of report FP_PDF_TEST_07 in the ABAP editor">
  <figcaption>The same section in the original report FP_PDF_TEST_07.</figcaption>
</figure>

## Wiring it into a print program

In your print program, you first need the form back as a PDF instead of sending it straight to the printer. Set these alongside your usual output and document parameters:

```abap
outputparams-connection = 'ADS_SSL'.   " ADS SSL connection name
outputparams-getpdf     = 'X'.         " return the PDF to the program
docparams-fillable      = abap_true.   " make the form interactive
```

Then pass the PDF you get back to the signing code above as `L_PDF`.

> With `getpdf` set, the form function module no longer handles **print preview or spooling** for you. You need to write that part yourself, using the signed `L_OUT`.
{: .callout .warning}

One more step: in the form itself (transaction **SFP**), set every field to **read-only**. Setting `fillable` makes the PDF interactive, and you don't want anyone typing into a document you've just signed.

## Helpful links

- [Installing and Configuring Credentials for ADS](https://help.sap.com/docs/SAP_NETWEAVER_750/d2e18615eb27460d9c0b6533aa01d8a0/4b95ae6426f26e83e10000000a421937.html?version=7.5.7) (SAP Help Portal)
- [2721714 – Prerequisites to use Digital Signature with ADS](https://userapps.support.sap.com/sap/support/knowledge/en/2721714) (SAP Knowledge Base Article)
- [Digital Signature in Adobe Forms](https://blogs.sap.com/2013/05/21/digital-signature-in-adobe-forms/) (SAP Blogs)
- [Introduction to Digital Signatures and SAP Interactive Forms by Adobe](https://blogs.sap.com/2009/04/22/introduction-to-digital-signatures-and-sap-interactive-forms-by-adobe/) (SAP Blogs)
- [What is a Digital Signature?](https://www.techtarget.com/searchsecurity/definition/digital-signature) (TechTarget)
