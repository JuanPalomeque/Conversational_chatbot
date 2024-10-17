# Conversational_chatbot using Telegram bot
## Introduction to chatbot programming and machine learning technines 

import logging

from telegram import ForceReply, Update
from telegram.ext import Application, CommandHandler, ContextTypes, MessageHandler, filters

from telegram import Bot
import asyncio

## Enable logging
logging.basicConfig(format="%(asctime)s - %(name)s - %(levelname)s - %(message)s", level=logging.INFO)

## Set higher logging level for httpx to avoid all GET and POST requests being logged
logging.getLogger("httpx").setLevel(logging.WARNING)
logger = logging.getLogger(__name__)


## Import libraries
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.preprocessing import LabelEncoder
from sklearn.naive_bayes import MultinomialNB

##----------------------preparing------------------------------------
import pandas as pd

df=pd.read_csv(r"D:\Users\User\PycharmProjects\Hola_Mundo\boot python\Base_bot.csv",sep=';')


vectorizador = CountVectorizer()

df['texto'].apply(lambda x: x.lower())
df['etiqueta'].apply(lambda x: x.lower())

X = df['texto']
X = vectorizador.fit_transform(X)

label_encoder = LabelEncoder()  # Transforma el texto en variables categóricas
y = df['etiqueta']
y = label_encoder.fit_transform(y)

## Inicializo y entreno el clasificador
clasificador = MultinomialNB()
clasificador.fit(X, y)

##---------------------------------Beggining of the program---------------------


def clasification(respuesta_usuario):
    '''función para predecir la categoria de la pregunta usando AA'''
    
    # Predigo
    new_X = vectorizador.transform([respuesta_usuario])   #respuesta del usuario debe ser por ej: ['hola']
    prediccion = clasificador.predict(new_X)

    return label_encoder.inverse_transform(prediccion)      #lo reconvierto a texto


respuestas=[]
respuesta=''
contador = 0
async def respuesta(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:


print(f'usuario: {update.message.text.lower()}')
clasificacion=clasification(update.message.text.lower())
global contador

if clasificacion == etiqueta_1:
    user = update.effective_user
    # {user.mention_html()}!'
    resp = [respuestas_etiqueta_1]
    respuesta = random.choice(resp)
elif clasificacion == etiqueta_2:
    resp = [respuestas_etiqueta_2]
    respuesta = random.choice(resp)
elif clasificacion == etiqueta_3:
    resp = [respuestas_etiqueta_3]
    respuesta = random.choice(resp)
elif clasificacion == etiqueta_4:
    resp = [respuestas_etiqueta_4]
    respuesta = random.choice(resp)
elif clasificacion == etiqueta_5:
    resp = [respuestas_etiqueta_5]
    respuesta = random.choice(resp)
elif clasificacion == etiqueta_6:
    resp = [respuestas_etiqueta_6]
    respuesta = random.choice(resp)
else:
     contador += 1
     if contador == 5:
        contador=0
        respuesta='respuesta extra'
     else:
         resp = ['respuestas extras']
         respuesta = random.choice(resp)

await update.message.reply_text(respuesta)
print(f'Bot: {respuesta}')


## Create the Application and pass it your bot's token.
application = Application.builder().token("TOKEN").build()


## on non command i.e message - respuesta the message on Telegram
application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, respuesta_usuario))

## Run the bot until the user presses Ctrl-C
application.run_polling(allowed_updates=Update.ALL_TYPES)


if __name__ == "__main__":
    main()


