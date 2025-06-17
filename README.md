import os
from telegram.ext import ApplicationBuilder, MessageHandler, filters
from handler import handle_document, handle_reply
from dotenv import load_dotenv

load_dotenv()
TOKEN = os.getenv("TELEGRAM_TOKEN")
print(f"TOKEN: {TOKEN}")

app = ApplicationBuilder().token(TOKEN).build()

app.add_handler(MessageHandler(filters.Document.ALL, handle_document))
app.add_handler(MessageHandler(filters.TEXT & (~filters.COMMAND), handle_reply))

if __name__ == "__main__":
    print("🤖 Bot sedang berjalan...")
    app.run_polling()
