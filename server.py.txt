from flask import Flask, request, jsonify
import requests
import os

app = Flask(__name__)

TELEGRAM_BOT_TOKEN = os.getenv("TELEGRAM_BOT_TOKEN")
TELEGRAM_CHAT_ID = os.getenv("TELEGRAM_CHAT_ID")

@app.route('/')
def home():
    return "Webhook server aktif 🚀", 200

@app.route('/webhook', methods=['POST'])
def webhook():
    data = request.get_json(force=True)
    print("📩 Data masuk dari TradingView:", data)

    # Ambil pesan dari alert TradingView
    message = data.get("message", "⚠️ Tidak ada pesan")

    # Kirim ke Telegram kalau variabel ada
    if TELEGRAM_BOT_TOKEN and TELEGRAM_CHAT_ID:
        url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
        payload = {"chat_id": TELEGRAM_CHAT_ID, "text": f"TradingView Alert:\n{message}"}
        requests.post(url, json=payload)

    return jsonify({"status": "ok", "message_diterima": message}), 200


if __name__ == '__main__':
    app.run(host="0.0.0.0", port=10000)
