# Ghatbot.-Launch 
import os
import google.generativeai as genai
from flask import Flask, render_template, request, jsonify

# --- Configuration ---
# It's recommended to set the API key as an environment variable.
try:
    GEMINI_API_KEY = os.environ["GEMINI_API_KEY"]
except KeyError:
    print("Error: GEMINI_API_KEY environment variable not set.")
    exit()

genai.configure(api_key=GEMINI_API_KEY)

# --- Flask App Initialization ---
app = Flask(__name__)

# --- Model and Chat Initialization ---
# Initialize the generative model
model = genai.GenerativeModel('gemini-1.5-flash')

# Start a chat session with an empty history
chat = model.start_chat(history=[])

# --- Routes ---
@app.route("/")
def index():
    """Renders the main chat page."""
    return render_template("index.html")

@app.route("/send_message", methods=["POST"])
def send_message():
    """Receives a user message, sends it to Gemini, and returns the response."""
    try:
        user_message = request.json["message"]
        
        # Send message to the model and get the response
        response = chat.send_message(user_message)
        
        # Return the bot's response as JSON
        return jsonify({"response": response.text})

    except Exception as e:
        # Handle potential errors during the API call
        print(f"An error occurred: {e}")
        return jsonify({"error": "An error occurred while processing your request."}), 500

# --- Main Execution ---
if __name__ == "__main__":
    app.run(debug=True)
