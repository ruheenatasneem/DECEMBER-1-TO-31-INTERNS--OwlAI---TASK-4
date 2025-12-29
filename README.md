# DECEMBER-1-TO-31-INTERNS--OwlAI---TASK-4
Build a Basic REST API  using python 


# Flask CRUD REST API Documentation

## 📌 Project Objective

Develop a RESTful API using **Flask** that supports full **CRUD (Create, Read, Update, Delete)** operations with database integration and proper error handling.

---

## 🛠️ Tech Stack

* **Backend Framework:** Flask (Python)
* **Database:** SQLite
* **ORM / DB Access:** sqlite3
* **API Testing Tool:** Postman / curl

---

## 📂 Project Structure

```
project-folder/
│
├── app.py               # Main Flask application
├── database.db          # SQLite database
├── requirements.txt     # Project dependencies
└── README.md            # API documentation
```

---

## 🗄️ Database Schema

**Table Name:** `items`

| Column   | Type    | Description                  |
| -------- | ------- | ---------------------------- |
| id       | INTEGER | Primary Key (Auto Increment) |
| name     | TEXT    | Item name                    |
| price    | REAL    | Item price                   |
| quantity | INTEGER | Available quantity           |

---

## 🔗 API Endpoints

### 1️⃣ Create Item

**Endpoint:** `POST /items`

**Request Body (JSON):**

```json
{
  "name": "Pen",
  "price": 10.5,
  "quantity": 20
}
```

**Success Response:**

```json
{
  "message": "Item created successfully"
}
```

**Status Code:** `201 Created`

---

### 2️⃣ Get All Items

**Endpoint:** `GET /items`

**Response:**

```json
[
  {
    "id": 1,
    "name": "Pen",
    "price": 10.5,
```


4️⃣ Get Item by ID

GET /items/<id>

Response

{

  "id": 1,
  
  "name": "Laptop",
  
  "description": "Dell Inspiron"
  
}


Error Response

{

  "error": "Item not found"
  
}


Status Code: 404 Not Found

5️⃣ Update Item


PUT /items/<id>

Request Body

{

  "name": "Updated Laptop",
  
  "description": "HP Pavilion"
  
}

Response
{

  "message": "Item updated successfully"
  
}


Status Code: 200 OK

6️⃣ Delete Item

DELETE /items/<id>

Response

{

  "message": "Item deleted successfully"
  
}


Status Code: 200 OK

⚠️ Error Handling

Scenario	Response

Missing fields	400 Bad Request

Item not found	404 Not Found

Invalid request	500 Internal Server Error

Example:

{

  "error": "Invalid input data"
  
}

🧪 Testing the API

Using Postman

Open Postman

Select HTTP Method (GET/POST/PUT/DELETE)

Enter URL (e.g., http://127.0.0.1:5000/items)

Add JSON body for POST/PUT

Click Send

Using cURL 

curl http://127.0.0.1:5000/items   




coding part  



from flask import Flask, request, jsonify 

import sqlite3

app = Flask(__name__) 

DATABASE = "database.db"

# -------- Database Connection -------- 

def get_db_connection(): 

    conn = sqlite3.connect(DATABASE) 
    
    conn.row_factory = sqlite3.Row   # IMPORTANT 
    
    return conn

# -------- Create Table -------- 

def create_table(): 

    conn = get_db_connection() 
    
    conn.execute(""" 
    
        CREATE TABLE IF NOT EXISTS items ( 
        
            id INTEGER PRIMARY KEY AUTOINCREMENT, 
            
            name TEXT NOT NULL, 
            
            description TEXT
        )
    """)
    conn.commit() 
    
    conn.close()

# -------- Home Route -------- 

@app.route("/") 

def home():

    return jsonify({"message": "Flask CRUD API is running"})

# -------- CREATE -------- 

@app.route("/items", methods=["POST"]) 

def create_item(): 

    data = request.get_json()

    if not data or not data.get("name"): 
    
        return jsonify({"error": "Name is required"}), 400

    conn = get_db_connection() 
    
    conn.execute( 
    
        "INSERT INTO items (name, description) VALUES (?, ?)", 
        
        (data["name"], data.get("description"))
    )
    conn.commit() 
    
    conn.close()

    return jsonify({"message": "Item created successfully"}), 201

# -------- READ ALL -------- 

@app.route("/items", methods=["GET"]) 

def get_items(): 

    conn = get_db_connection() 
    
    items = conn.execute("SELECT * FROM items").fetchall() 
    
    conn.close()

    return jsonify([dict(item) for item in items]), 200

# -------- READ ONE -------- 

@app.route("/items/<int:id>", methods=["GET"]) 

def get_item(id): 

    conn = get_db_connection() 
    
    item = conn.execute( 
    
        "SELECT * FROM items WHERE id = ?", (id,) 
        
    ).fetchone() 
    
    conn.close()

    if item is None: 
    
        return jsonify({"error": "Item not found"}), 404

    return jsonify(dict(item)), 200

# -------- UPDATE -------- 

@app.route("/items/<int:id>", methods=["PUT"]) 

def update_item(id): 

    data = request.get_json() 
    
    conn = get_db_connection()

    item = conn.execute( 
    
        "SELECT * FROM items WHERE id = ?", (id,) 
        
    ).fetchone()

    if item is None: 
    
        conn.close() 
        
        return jsonify({"error": "Item not found"}), 404

    conn.execute( 
    
        "UPDATE items SET name = ?, description = ? WHERE id = ?",
        (
            data.get("name", item["name"]), 
            
            data.get("description", item["description"]), 
            
            id
        )
    )
    conn.commit() 
    
    conn.close()

    return jsonify({"message": "Item updated successfully"}), 200

# -------- DELETE -------- 

@app.route("/items/<int:id>", methods=["DELETE"]) 

def delete_item(id): 

    conn = get_db_connection() 
    
    item = conn.execute( 
    
        "SELECT * FROM items WHERE id = ?", (id,) 
        
    ).fetchone()

    if item is None: 
    
        conn.close() 
        
        return jsonify({"error": "Item not found"}), 404

    conn.execute("DELETE FROM items WHERE id = ?", (id,)) 
    
    conn.commit() 
    
    conn.close()

    return jsonify({"message": "Item deleted successfully"}), 200

if __name__ == "__main__": 

    create_table() 
    
    app.run(debug=True)   


    README.md 


    # Flask CRUD REST API

## Base URL 

http://127.0.0.1:5000

---

## 1. Create Item 

POST /items

Request Body (JSON):
{
  "name": "Laptop", 
  
  "description": "Dell Inspiron"
}

Response: 

201 Created

---

## 2. Get All Items 

GET /items

Response:
[
  { 
  
    "id": 1, 
    
    "name": "Laptop", 
    
    "description": "Dell Inspiron"
  } 
  
]

---

## 3. Get Single Item 

GET /items/1

Response:
{ 

  "id": 1, 
  
  "name": "Laptop", 
  
  "description": "Dell Inspiron"
}

---

## 4. Update Item 

PUT /items/1

Request Body:
{ 

  "name": "Laptop Updated", 
  
  "description": "HP Pavilion"
}

Response: 

200 OK

---

## 5. Delete Item 

DELETE /items/1

Response: 

200 OK

---

## Error Handling 

- 400 → Bad Request
- 
- 404 → Item Not Found

---

## Testing Tools
- Postman
- Curl
- Thunder Client (VS Code)


out put  








<img width="1366" height="768" alt="Screenshot (222)" src="https://github.com/user-attachments/assets/e27278bd-7916-418f-8888-2165873cd049" />   











<img width="1366" height="768" alt="Screenshot (223)" src="https://github.com/user-attachments/assets/49d675eb-aa27-4f7e-bab0-319a854dd3d4" /> 







video link: https://youtu.be/4AsRa4t8TRI

github link: https://github.com/ruheenatasneem/DECEMBER-1-TO-31-INTERNS--OwlAI---TASK-4











