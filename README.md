# Instagram-Thrift-Creator-Store
To design a robust database for a growing thrift and handmade business, we need a model that balances the uniqueness of "one-of-a-kind" items with the repeatable nature of handmade goods.

The following design uses a Product-Variant approach to handle inventory flexibility and a junction table for orders to allow multiple items per purchase.

## Database Schema
A. Product Catalog

Since thrifted items (unique) and handmade items (batch) share many traits but differ in inventory, we use a single Products table with a type flag and a ProductVariants table to track specifics.

🛠️ Data Dictionary
-------------------

### 1\. Products & Inventory

*   **Product:** The high-level definition. The product\_type flag distinguishes between Thrift and Handmade logic.
    
*   **Product\_Variant:** This table handles specific attributes.
    
    *   **For Thrift:** stock\_quantity is always 1. item\_condition is mandatory to describe wear and tear.
        
    *   **For Handmade:** stock\_quantity can be > 1. Multiple variants (e.g., Red vs. Blue) can exist for a single handmade product.
        
*   **Category:** Groups items for easier browsing (e.g., "Vintage Jackets", "Handmade Jewelry").
    

### 2\. Customers

*   **Customer:** Since the business operates on social media, we store ig\_handle and whatsapp\_number alongside traditional contact info to ensure the owner can reach out via the customer's preferred platform.
    

### 3\. Orders & Fulfillment

*   **Order:** Tracks the financial and logistics state. We separate payment\_status from shipping\_status because handmade items may be paid for but require lead time for production before shipping.
    
*   **Order\_Item:** A junction table between Orders and Product Variants. It captures unit\_price\_at\_sale to protect historical revenue data against future price changes.
    

💡 Key Design Considerations
----------------------------

### 1\. How it handles Thrift vs. Handmade

The design uses a **One-to-Many** relationship between PRODUCT and PRODUCT\_VARIANT.

*   A **Thrift** item is treated as a Product with exactly one Variant that has a quantity of 1.
    
*   A **Handmade** item is a Product that can have multiple Variants (different sizes/colors) with quantities reflecting current stock levels.
    

### 2\. Payment & Shipping Representation

The ORDER entity acts as a central hub for status tracking:

*   **Payment Status:** Allows the owner to confirm bank transfers/link payments before beginning fulfillment.
    
*   **Shipping Status:** Updates the customer from "Processing" (making the item/packaging) to "Delivered."
    

### 3\. Order Flexibility

*   A Customer can place multiple orders (1:N).
    
*   An Order can contain multiple products (via ORDER\_ITEM).
    
*   The quantity field in ORDER\_ITEM supports batch purchases of handmade goods.
    

🚀 Scalability
--------------

This design is normalized to **3rd Normal Form (3NF)**. It avoids data redundancy (like repeating customer addresses in every order row) and ensures that if a product is deleted or its price is changed, the historical integrity of past orders remains intact.