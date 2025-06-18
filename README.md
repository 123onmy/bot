from aiogram import Bot, Dispatcher, types
from aiogram.filters import CommandStart
import asyncio

# 🔐 Данные твоего бота и канала
BOT_TOKEN = "7758394990:AAE4gtnZj3SmtAJaaGe6c-YvJjV0oYaYuMU"
CHANNEL_USERNAME = "@wbhlebnikova"
ADMIN_ID = 1334763706  # твой Telegram ID

# ⚙️ Создание бота и диспетчера
bot = Bot(token=BOT_TOKEN)
dp = Dispatcher()

# 🟢 Команда /start
@dp.message(CommandStart())
async def start(msg: types.Message):
    await msg.answer("Привет! Отправь фото для оценки. Убедись, что ты подписан на наш канал.")

# 📷 Обработка фото
@dp.message()
async def handle_photo(msg: types.Message):
    if not msg.photo:
        await msg.reply("Пожалуйста, пришли фото.")
        return

    try:
        member = await bot.get_chat_member(CHANNEL_USERNAME, msg.from_user.id)
        if member.status not in ("member", "creator", "administrator"):
            raise Exception("Not subscribed")
    except:
        await msg.reply("Пожалуйста, сначала подпишись на наш канал: " + CHANNEL_USERNAME)
        return

    await msg.reply("Фото получено. Спасибо!")

    # Пересылаем фото админу
    await bot.send_photo(
        chat_id=ADMIN_ID,
        photo=msg.photo[-1].file_id,
        caption=f"Фото от @{msg.from_user.username or msg.from_user.first_name} (ID {msg.from_user.id})"
    )

# 🚀 Запуск бота
async def main():
    await bot.delete_webhook(drop_pending_updates=True)
    await dp.start_polling(bot)

@dp.message()
async def handle_photo(msg: types.Message):
    print(f"Получено сообщение от {msg.from_user.id}")  # Добавим лог

    if not msg.photo:
        await msg.reply("Пожалуйста, пришли фото.")
        return
    ...


if __name__ == "__main__":
    asyncio.run(main())
