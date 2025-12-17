# Demo Video Script (Product API JSON + XML)

Approx. length: 2–3 minutes. Edit wording/timing as you like.

## 0) Start server (off-camera or quick flash)

- Terminal: `python manage.py runserver`

## 1) Intro (5–10s)

- “This is the Product API. It supports JSON and XML. Docs are at /api/docs.”

## 2) Show docs (10–15s)

- Browser: `http://127.0.0.1:8000/api/docs/`
- “You can see endpoints for list, create, get, update, delete, and content negotiation via Accept or ?format=.”

## 3) List products – JSON (10s)

- Postman GET `http://127.0.0.1:8000/api/products/`
- Header: `Accept: application/json`
- “Here’s the product list in JSON.”

## 4) List products – XML (10s)

- Same request with `?format=xml` or header `Accept: application/xml`
- “Same list in XML.”

## 5) Create product – JSON (20s)

- Postman POST `http://127.0.0.1:8000/api/products/`
- Header: `Content-Type: application/json`
- Body (raw JSON):

  ```json
  {"name":"Demo Bread","price":5.25,"stock":10,"ingredients":"flour, sugar","is_active":true}
  ```

- “Create returns 201 with the new product and its id.”

## 6) Get the new product – JSON (10s)

- GET `http://127.0.0.1:8000/api/products/<new_id>/`
- Header: `Accept: application/json`
- “Here’s the created product.”

## 7) Update product – XML (20s)

- PUT `http://127.0.0.1:8000/api/products/<new_id>/?format=xml`
- Header: `Content-Type: application/xml`
- Body (raw XML):

  ```xml
  <product><stock>5</stock><is_active>true</is_active></product>
  ```

- “Updated via XML; response shows the new values.”

## 8) Delete product (10s)

- DELETE `http://127.0.0.1:8000/api/products/<new_id>/`
- “Delete succeeds; if it were referenced in sales, it would archive instead.”

## 9) Wrap-up (5–10s)

- “Both JSON and XML are supported, with validation on required fields. Docs at /api/docs.”

Optional: Show a quick validation error (e.g., POST without `name`) to demonstrate HTTP 400 with error details.
