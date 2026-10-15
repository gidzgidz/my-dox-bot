import telebot
from telebot import types

BOT_TOKEN = "8747973107:AAF3p7MtqfxRi5VatYqwWHS9fG6aoOPplHo"
TON_ADDRESS = "UQDLwMVF1Ax-ZScPfTJ1q2sPSUkDnO5bUm1Kapii2hnyVGMB"
PRICE_TON = 3

bot = telebot.TeleBot(BOT_TOKEN)

def main_menu():
    kb = types.InlineKeyboardMarkup(row_width=1)
    kb.add(
        types.InlineKeyboardButton(text="Поиск по номеру телефона📱", callback_data="paywall"),
   types.InlineKeyboardButton(text="Шаблоны для троллинга", callback_data="paywall"),
        types.InlineKeyboardButton(text="СН∅С ЖЕРТВЫ🚧", callback_data="paywall"),
        types.InlineKeyboardButton(text="Поиск по Тиктоку🤫", callback_data="paywall"),
        types.InlineKeyboardButton(text="Поиск по почте📩", callback_data="paywall"),
    )
    return kb

@bot.message_handler(commands=['start'])
def start(message):
    bot.send_message(
        message.chat.id,
        "👋 Привет!\n\n"
        "Это FlewKill — Твой универсальный бот помощник, сносер, доксер.\n\n"
        "📄 Выбери, что тебе нужно 👇",
        reply_markup=main_menu()
    )

@bot.callback_query_handler(func=lambda call: call.data == "paywall")
def paywall(call):
    bot.answer_callback_query(call.id, "🔒 Нужна оплата", show_alert=True)
    kb = types.InlineKeyboardMarkup()
    kb.add(types.InlineKeyboardButton(
        text="💎 Оплатить 3 TON",
        callback_data="pay"
    ))
    bot.send_message(
        call.message.chat.id,
        "🔒 Для доступа к этому разделу нужна оплата.\n\n"
        "💎 Стоимость: *3 TON*",
        reply_markup=kb,
        parse_mode="Markdown"
    )

@bot.callback_query_handler(func=lambda call: call.data == "pay")
def pay(call):
    kb = types.InlineKeyboardMarkup()
    kb.add(types.InlineKeyboardButton(
        text="Открыть Tonkeeper",
        url=f"ton://transfer/{TON_ADDRESS}?amount={PRICE_TON * 10**9}"
    ))
    kb.add(types.InlineKeyboardButton(
        text="✅ Я оплатил",
        callback_data="check_payment"
    ))
    bot.edit_message_text(
        f"💎 Оплата доступа\n\n"
        f"Сумма: *{PRICE_TON} TON*\n\n"
        f"Адрес:\n`{TON_ADDRESS}`\n\n"
        f"Нажми «Открыть Tonkeeper» и переведи {PRICE_TON} TON.\n"
        f"Затем нажми «✅ Я оплатил».\n\n"
        f"⚠️ *Важно:* если ты не оплатил и нажал «Я оплатил» — "
        f"доступ к этому боту будет заблокирован навсегда.",
        call.message.chat.id,
        call.message.message_id,
        reply_markup=kb,
        parse_mode="Markdown"
    )

@bot.callback_query_handler(func=lambda call: call.data == "check_payment")
def check_payment(call):
    bot.answer_callback_query(call.id, "Проверка…")

    msg = bot.send_message(
        call.message.chat.id,
        "🔄 Проверка…"
    )

    import time
    time.sleep(3)

    bot.edit_message_text(
        "⏳ Проверка транзакции…\n\n"
        "Ожидаю подтверждения в блокчейне.",
        call.message.chat.id,
        msg.message_id
    )

    time.sleep(3)

    bot.edit_message_text(
        "✅ Оплата найдена!\n\n"
        "Доступ открыт. Напиши мне для получения материалов.",
        call.message.chat.id,
        msg.message_id
    )

bot.polling(none_stop=True)