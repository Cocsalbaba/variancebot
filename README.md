from telegram import Update
from telegram.ext import Application, CommandHandler, MessageHandler, filters, CallbackContext
import math

# ذخیره‌سازی اعداد ورودی کاربران
user_data = {}

# فرمان /start برای آغاز کار ربات
async def start(update: Update, context: CallbackContext) -> None:
    await update.message.reply_text("سلام! اعداد خود را با فاصله وارد کنید:")

# ذخیره اعداد وارد شده توسط کاربر
async def get_numbers(update: Update, context: CallbackContext) -> None:
    user_id = update.message.from_user.id
    numbers = list(map(float, update.message.text.split()))

    # ذخیره‌سازی اعداد برای هر کاربر
    user_data[user_id] = numbers
    await update.message.reply_text("اعداد شما ذخیره شدند. از دستور /result برای دیدن نتیجه استفاده کنید.")

# محاسبه و نمایش نتیجه
async def result(update: Update, context: CallbackContext) -> None:
    user_id = update.message.from_user.id

    if user_id not in user_data:
        await update.message.reply_text("لطفاً ابتدا اعداد خود را وارد کنید.")
        return

    numbers = user_data[user_id]
    mean = sum(numbers) / len(numbers)
    variance = sum((x - mean) ** 2 for x in numbers) / len(numbers)
    std_deviation = math.sqrt(variance)
    mean_divided_by_two = mean / 2

    if std_deviation < mean_divided_by_two:
        await update.message.reply_text("تلاشت موفق آمیز بوده")
    else:
        await update.message.reply_text("تلاشت موفق آمیز نبوده")

# راه‌اندازی ربات
def main() -> None:
    # توکن ربات خود را در اینجا وارد کنید
    application = Application.builder().token("7269797072:AAH9f_uql5sbDfDiegvZJ2WA9WDwVkPlYLE").build()

    # هندلرها برای فرمان‌ها و پیام‌ها
    application.add_handler(CommandHandler("start", start))
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, get_numbers))
    application.add_handler(CommandHandler("result", result))

    # شروع ربات
    application.run_polling()

if __name__ == '__main__':
    main()
