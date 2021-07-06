# dzer-hamar
import telebot
from pyowm import OWM
from pyowm.utils.config import get_default_config
from telebot import types

TOKEN = "1742160613:AAEpQ2cK-Sw-57QuMttololjalQdgm-bWJU"
bot = telebot.AsyncTeleBot(token=TOKEN)

config_dict = get_default_config()
config_dict['language'] = 'ru'
owm = OWM("87395221864d4fa7a9319a0afc33b986", config_dict)


@bot.message_handler(commands=["get_weather", "weather"])
class Starter_Weather:
    def __init__(self, message):
        self.send = bot.send_message(message.chat.id, "Which City do you want??")


@bot.message_handler(commands=["get_info", "info"])
class Starter_Info:
    def __init__(self, message):
        markup_inline = types.InlineKeyboardMarkup()
        item_yes = types.InlineKeyboardButton(text="Ayo", callback_data="yes")
        item_no = types.InlineKeyboardButton(text="Che", callback_data="no")
        markup_inline.add(item_yes, item_no)
        bot.send_message(message.chat.id, "Uzumes imanas qo masin???🤔", reply_markup=markup_inline)


@bot.callback_query_handler(func=lambda call: True)
class Answer:
    def __init__(self, call):
        if call.data == "yes":
            bot.send_message(call.message.chat.id, "Mi ban gri")
        elif call.data == "no":
            bot.send_message(call.message.chat.id, "Lav de ete ches uzum 😢, de es gnaci davay")


@bot.message_handler(commands=["get_info", "ID"])
class Id:
    def __init__(self, message):
        self.answer = bot.send_message(message.chat.id, f"Your ID: {message.from_user.id}")


@bot.message_handler(commands=["get_info", "Name"])
class Name:
    def __init__(self, message):
        bot.send_message(message.chat.id, f"Your Name: {message.from_user.first_name} {message.from_user.last_name}")


@bot.message_handler(content_types="text")
class Do_action:
    def __init__(self, message):
        self.text = message.text
        self.mgr = owm.weather_manager()
        observation = self.mgr.weather_at_place(self.text)
        self.w = observation.weather
        self.w1 = self.w.temperature('celsius')["temp"]
        self.w = observation.weather.detailed_status
        self.do = bot.send_message(message.chat.id, f"Сейчас {self.w} \nTemp: {self.w1}°")


bot.polling(none_stop=True, interval=0)
