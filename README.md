# Build-a-REST-API-with-Flask-Objective
Create a REST API that manages user data
from flask import Flask, jsonify, request

app = Flask(__name__)

# In-memory data storage (replace with a database in production)
users = {}
next_id = 1

# GET /users - Retrieve all users
@app.route('/users', methods=['GET'])
def get_users():
    return jsonify(list(users.values())), 200

# GET /users/<id> - Retrieve a specific user
@app.route('/users/<int:user_id>', methods=['GET'])
def get_user(user_id):
    user = users.get(user_id)
    if user:
        return jsonify(user), 200
    return jsonify({"error": "User not found"}), 404

# POST /users - Create a new user
@app.route('/users', methods=['POST'])
def create_user():
    global next_id
    data = request.get_json()
    if not data or not data.get('name') or not data.get('email'):
        return jsonify({"error": "Name and email are required"}), 400
    user = {"id": next_id, "name": data['name'], "email": data['email']}
    users[next_id] = user
    next_id += 1
    return jsonify(user), 201

# PUT /users/<id> - Update an existing user
@app.route('/users/<int:user_id>', methods=['PUT'])
def update_user(user_id):
    user = users.get(user_id)
    if not user:
        return jsonify({"error": "User not found"}), 404
    data = request.get_json()
    if data.get('name'):
        user['name'] = data['name']
    if data.get('email'):
        user['email'] = data['email']
    return jsonify(user), 200

# DELETE /users/<id> - Delete a user
@app.route('/users/<int:user_id>', methods=['DELETE'])
def delete_user(user_id):
    if user_id in users:
        del users[user_id]
        return jsonify({"message": "User deleted"}), 200
    return jsonify({"error": "User not found"}), 404

if __name__ == '__main__':
    app.run(debug=True)
