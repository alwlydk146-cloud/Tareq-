import logging
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes
import yt_dlp
import os

# --- إعداداتك الخاصة ---
BOT_TOKEN = '8686110725:AAEZg2MFGn5cjqP53IBrNBmMJsfY2ZgBXYI'
MY_USER = '@TJV99' # ضع يوزرك هنا
CHANNEL_ID = '@fhkthdujd' # ضع معرف قناتك هنا (مثال: @RashidChannel)
# -----------------------

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_id = update.effective_user.id
    
    # 1. التحقق من الاشتراك الإجباري
    try:
        member = await context.bot.get_chat_member(chat_id=CHANNEL_ID, user_id=user_id)
        if member.status in ['left', 'kicked']:
            keyboard = [[InlineKeyboardButton("الاشتراك في القناة 📢", url=f"https://t.me/{CHANNEL_ID[1:]}")]]
            reply_markup = InlineKeyboardMarkup(keyboard)
            await update.message.reply_text(
                f"⚠️ عذراً! يجب عليك الاشتراك في القناة أولاً لاستخدام البوت.\n\nبعد الاشتراك أرسل /start مرة أخرى.",
                reply_markup=reply_markup
            )
            return
    except Exception as e:
        print(f"خطأ في التحقق: {e}")

    # 2. رسالة الترحيب (نفس شكل الصورة التي أرفقتها)
    welcome_text = (
        f"👋 أهلاً بك يا {update.effective_user.first_name}\n"
        "في بوت تحميل الكل\n\n"
        "نبذة :\n"
        "يدعم التحميل من تيك توك، سناب، يوتيوب، انستقرام والمزيد...\n"
        "بدون علامة مائية ✅\n\n"
        f"للتواصل مع الصانع : {MY_USER}\n\n"
        "👇 أرسل رابط الفيديو الآن"
    )
    await update.message.reply_text(welcome_text)

async def download_video(update: Update, context: ContextTypes.DEFAULT_TYPE):
    url = update.message.text
    if not url.startswith("http"):
        return

    msg = await update.message.reply_text("جاري التحميل... انتظر قليلاً ⏳")
    
    try:
        ydl_opts = {
            'format': 'best',
            'outtmpl': 'video.mp4',
            'quiet': True
        }
        
        with yt_dlp.YoutubeDL(ydl_opts) as ydl:
            ydl.download([url])
            
        await update.message.reply_video(video=open('video.mp4', 'rb'), caption="تم التحميل بواسطة بوتك ✅")
        await msg.delete()
        os.remove('video.mp4') # حذف الملف بعد الإرسال لتوفير المساحة
        
    except Exception as e:
        await msg.edit_text(f"❌ حدث خطأ: تأكد من الرابط أو حاول لاحقاً.")
        print(f"Error: {e}")

def main():
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, download_video))
    
    print("البوت يعمل الآن... اذهب لتليجرام وجربه!")
    app.run_polling()

if __name__ == '__main__':
    main()
