---
layout: post
title: "Getting Started with ABAP RAP"
date: 2025-01-15
categories: [RAP, ABAP]
read_time: 5
featured: true
---

RESTful ABAP Programming (RAP) is the modern way to build applications in SAP. In this post, I'll give you a quick overview of what RAP is and why you should learn it.

## What is RAP?

RAP stands for **RESTful ABAP Programming Model**. It's SAP's recommended approach for building:

- Fiori Elements apps
- Web APIs
- Transactional apps on S/4HANA and BTP

## Why Learn RAP?

Here are a few reasons:

1. **Future of ABAP** — SAP is investing heavily in RAP
2. **Less Code** — Built-in features reduce boilerplate
3. **Fiori Ready** — Seamless integration with Fiori Elements
4. **Cloud Compatible** — Works on BTP ABAP Environment

## Key Components

RAP consists of three main parts:

### 1. CDS Views (Data Model)

```abap
define root view entity ZI_Product
  as select from zproduct
{
  key product_id   as ProductId,
      product_name as ProductName,
      price        as Price
}

### 2. Behavior Definition

managed implementation in class zbp_i_product unique;

define behavior for ZI_Product
{
  create;
  update;
  delete;
}

### 3. Behavior Implementation

CLASS zbp_i_product DEFINITION PUBLIC ABSTRACT FINAL
  FOR BEHAVIOR OF zi_product.
ENDCLASS.

CLASS zbp_i_product IMPLEMENTATION.
ENDCLASS.

### Next Steps

In upcoming posts, I'll dive deeper into:

Creating your first RAP application
Validations and determinations
Actions and functions
Draft handling
Stay tuned!