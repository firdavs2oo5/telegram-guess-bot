from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, ContextTypes, filters

import random

TOKEN = "7872810895:AAEnfHaF3A5VUjU_oKehMNHL3_NL3wdyFKc"

# Словарь для хранения случайного числа для каждого пользователя
user_data = {}

# Команда /start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Привет! Это игра 'Угадай число'. Напиши 'игра', чтобы начать."
    )

# Обработка текста от пользователя
async def play_game(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id
    user_input = update.message.text.lower()

    if user_input == "игра":
        n = 50  # максимальное число
        secret_number = random.randint(1, n)
        user_data[user_id] = secret_number
        await update.message.reply_text(f"Игра началась! Я загадал число от 1 до {n}. Попробуй угадать!")
    elif user_id in user_data:
        try:
            guess = int(user_input)
            secret_number = user_data[user_id]
            if guess < secret_number:
                await update.message.reply_text("Загаданное число больше.")
            elif guess > secret_number:
                await update.message.reply_text("Загаданное число меньше.")
            else:
                await update.message.reply_text(f"Поздравляю! Ты угадал число {guess}!")
                del user_data[user_id]  # игра окончена
        except ValueError:
            await update.message.reply_text("Пожалуйста, введи число.")
    else:
        await update.message.reply_text("Напиши 'игра', чтобы начать новую игру.")

# Запуск бота
if __name__ == "__main__":
    app = ApplicationBuilder().token(TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, play_game))

    print("Бот запущен")
    app.run_polling()
