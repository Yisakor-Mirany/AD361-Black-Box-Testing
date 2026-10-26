# AD361 Lab: Black-Box Testing

## Overview

This lab demonstrates three black-box testing techniques: equivalence partitioning, decision table testing, and state transition testing. Test cases are designed from user-visible behavior without examining the website's source code. All outcomes below are **expected results**, not claims that tests have been executed.

## Step 1: Website Selection

- **Website:** Amazon
- **URL:** https://www.amazon.com/
- **Selected features:** Product Search and Shopping Cart (including adding products and starting checkout)
- **Testing approach:** Black-box testing only; no source code inspection.

> **Assumptions:** Amazon's interface and product-specific controls can vary. For add-to-cart tests, choose a product listing that visibly offers required options (such as size) and a quantity selector when needed. “Invalid” below means an input or action that does not meet the test's intended use; the site's actual response must be verified if tests are executed.

## Step 2: Equivalence Partitioning

**Feature:** Product Search

Equivalence partitioning groups inputs expected to produce similar behavior. One representative value is chosen from each partition.

| Test ID | Partition | Classification | Representative input | Expected result |
|---|---|---|---|---|
| EP01 | Common product keyword | Valid | `laptop` | Search results relevant to laptops are shown. |
| EP02 | Multiword product phrase | Valid | `wireless headphones` | Results relevant to the phrase are shown. |
| EP03 | Brand keyword | Valid | `Samsung` | Results related to the brand are shown. |
| EP04 | Empty search field | Invalid | Empty string | No meaningful product-query results are submitted; the site may remain on or navigate to a default page. |
| EP05 | Punctuation-only query | Invalid | `@#$%^` | No relevant product matches, or the site handles the query without crashing. |
| EP06 | Unlikely-to-match text | Invalid | `zzqvnotaproduct987654` | A no-results or low-relevance-results response is shown without an application error. |

**Why this helps:** Instead of testing every possible search string, we sample distinct categories of input. The expected results are hypotheses to check against the live interface.

## Step 3: Decision Table Testing

**Feature:** Add to Cart

**Conditions**

- C1: Is the product available to purchase?
- C2: Have all required product options been selected?
- C3: Is the requested quantity valid and selectable?

**Actions**

- A1: Add the product to the cart.
- A2: Prevent the add-to-cart action or prompt for correction.

### Decision Table

| Conditions / Actions | R1 | R2 | R3 | R4 | R5 | R6 | R7 | R8 |
|---|---|---|---|---|---|---|---|---|
| C1: Available | Y | Y | Y | Y | N | N | N | N |
| C2: Options selected | Y | Y | N | N | Y | Y | N | N |
| C3: Quantity valid | Y | N | Y | N | Y | N | Y | N |
| A1: Add to cart | Y | N | N | N | N | N | N | N |
| A2: Prevent/prompt | N | Y | Y | Y | Y | Y | Y | Y |

### Decision Table Test Cases

| Test ID | Rule | Test setup / input | Expected result |
|---|---|---|---|
| DT01 | R1 | In-stock item; required options selected; valid quantity of 1 | Item is added to cart. |
| DT02 | R2 | In-stock item; required options selected; invalid/unselectable quantity | Item is not added; quantity must be corrected. |
| DT03 | R3 | In-stock item; required option omitted; valid quantity | Item is not added until the option is selected. |
| DT04 | R4 | In-stock item; required option omitted; invalid quantity | Item is not added. |
| DT05 | R5 | Out-of-stock item; options selected; valid quantity | Item cannot be added for immediate purchase. |
| DT06 | R6 | Out-of-stock item; options selected; invalid quantity | Item cannot be added. |
| DT07 | R7 | Out-of-stock item; option omitted; valid quantity | Item cannot be added. |
| DT08 | R8 | Out-of-stock item; option omitted; invalid quantity | Item cannot be added. |

**Note:** Some invalid combinations may be impossible to enter through the interface. In that case, record the blocked control as the observed behavior rather than bypassing the UI.

## Step 4: State Transition Testing

**Feature:** Shopping Cart

### States

- **S1 — Empty Cart:** No items in the cart.
- **S2 — Cart Has Items:** At least one item is in the cart.
- **S3 — Checkout Started:** The user has entered the checkout flow.

### Events

- **E1:** Add an available product.
- **E2:** Add another available product.
- **E3:** Remove the last item.
- **E4:** Begin checkout.
- **E5:** Attempt to remove an item from an empty cart.

### State Transition Table

| Transition ID | Current state | Event | Expected next state | Validity / expected behavior |
|---|---|---|---|---|
| T01 | S1 | E1: Add product | S2 | Valid; cart contains the product. |
| T02 | S2 | E2: Add another product | S2 | Valid; cart still contains items. |
| T03 | S2 (one item) | E3: Remove last item | S1 | Valid; cart becomes empty. |
| T04 | S2 | E4: Begin checkout | S3 | Valid when checkout requirements are met; sign-in may be requested. |
| T05 | S1 | E4: Attempt checkout | S1 | Invalid; checkout is unavailable for an empty cart. |
| T06 | S1 | E5: Attempt removal | S1 | Invalid; no item exists to remove. |

### State Transition Test Cases

| Test ID | Starting state | Steps | Expected result |
|---|---|---|---|
| ST01 | S1 | Add one available product. | Cart changes to S2. |
| ST02 | S2 | Add a second available product. | Cart remains in S2 and includes both items. |
| ST03 | S2 (one item) | Remove the only item. | Cart changes to S1. |
| ST04 | S2 | Select the checkout action. | Checkout flow begins (S3), possibly after sign-in. |
| ST05 | S1 | Look for and attempt to start checkout. | Checkout cannot proceed; cart remains S1. |
| ST06 | S1 | Attempt to remove an item. | No removal is possible; cart remains S1. |

**Limitations:** The state model is intentionally simplified. Real checkout may include sign-in, address, payment, and review states. These test cases do not complete a purchase.

## Conclusion

The three techniques provide complementary coverage:

- **Equivalence partitioning** samples different search-input categories.
- **Decision table testing** covers combinations of add-to-cart conditions.
- **State transition testing** checks how cart actions change the application's state.

This document is a **test design**. Actual results, pass/fail status, and screenshots should be added only after executing the tests.

## Submission Checklist

- [x] Select a website and identify features.
- [x] Document valid and invalid equivalence partitions with sample inputs and expected results.
- [x] Complete a decision table and derive test cases.
- [x] Identify states, events, valid/invalid transitions, and test cases.
- [ ] Create a public GitHub repository named `AD361-Black-Box-Testing`.
- [ ] Commit and push this `README.md`.
- [ ] Verify the repository is public and submit its URL.
