# Conversational_chatbot
Introduction to chatbot programming and machine learning technines 

import logging

from telegram import ForceReply, Update
from telegram.ext import Application, CommandHandler, ContextTypes, MessageHandler, filters

from telegram import Bot
import asyncio

# Enable logging
logging.basicConfig(
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s", level=logging.INFO
)
# set higher logging level for httpx to avoid all GET and POST requests being logged
logging.getLogger("httpx").setLevel(logging.WARNING)

logger = logging.getLogger(__name__)


from sklearn.feature_extraction.text import CountVectorizer
from sklearn.preprocessing import LabelEncoder
from sklearn.naive_bayes import MultinomialNB

# ----------------------preparing-----------------------------------------
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

# Inicializo y entreno el clasificador
clasificador = MultinomialNB()
clasificador.fit(X, y)

# ----------------------------------end-of-preparing----------------------------


#----------------------------------Beggining of the program---------------------



def clasification(respuesta):
    '''función para predecir la categoria de la pregunta usando AA'''
    # Predigo
    new_X = vectorizador.transform([respuesta])  #respuesta debe ser por ej: ['hola']
    prediccion = clasificador.predict(new_X)

    return label_encoder.inverse_transform(prediccion)      #lo reconvierto a texto

data = {'Que contas?':'Todo tranqui, vos?', }

respuestas=[]
respuesta=''
contador = 0
async def respuesta(update: Update, context: ContextTypes.DEFAULT_TYPE) -> None:

        import random
        print(f'Valen: {update.message.text.lower()}')
        clasificacion=clasification(update.message.text.lower())
        global contador

        if clasificacion == 'saludo':
            user = update.effective_user
            # {user.mention_html()}!'
            resp = ['Hola!', 'Buenas', 'Que contas?', 'Como anda la más linda?']
            respuesta = random.choice(resp)
        elif clasificacion == 'despedida':
            resp = ['chau', 'Chau linda!', 'Que triste que te vas...','Hablamos pronto']
            respuesta = random.choice(resp)
        elif clasificacion == 'risa':
            resp = ['jaja', 'jajaja', '😂']
            respuesta = random.choice(resp)
        elif clasificacion == 'insulto':
            resp = ['Vos seras', 'Que mala!', 'Pero callate !']
            respuesta = random.choice(resp)
        elif clasificacion == 'beso':
            resp = ['Besos para vos linda', '😘', 'Te mando 2 besos']
            respuesta = random.choice(resp)
        elif clasificacion == 'lindo':
            resp = ['Vos sos linda!', 'Sos terrible bombón', 'Sabias que sos linda?']
            respuesta = random.choice(resp)
        elif clasificacion == 'cordial':
            resp = ['Todo bien', 'todo tranqui', 'Acá andamos', 'mal porque no te veo...']
            respuesta = random.choice(resp)
        else:
             contador += 1
             if contador == 5:
                contador=0
                respuesta='Sabias que estas mas buena que un chocolate Cadbury?'
             else:
                 resp = ['que bueno', 'dale', 'Lo voy a pensar', 'y.. puede ser', 'si!', 'no se', '\U0001F642',
                         'sos re linda!','salimos?','y.. si']
                 respuesta = random.choice(resp)

        await update.message.reply_text(respuesta)
        print(f'Bot: {respuesta}')

       # await update.message.reply_text('HOLA')
       # user = update.effective_user
       # print(user.mention_html())
       # user_id = update.message.from_user.id
       # print(user_id)

       #YO: 1546945204
       #YO: <a href="tg://user?id=1546945204">Juan</a>

    #elif not update.message.text.lower().find('jaja'):
     #   await update.message.reply_text('jaja')

    #if 'gracias' in update.message.text.lower():
     #   await update.message.reply_text('de nada !')

    #elif 'Chau' in update.message.text or 'Adios' in update.message.text:
     #   await update.message.reply_text('Chau linda!')

    #else:
     #   await update.message.reply_text('Sos re linda! \U0001F642')



def main() -> None:

    # Create the Application and pass it your bot's token.
    application = Application.builder().token("2011743566:AAHVRnVdBq059q3nlK1adsr4yiODbOEXyXw").build()


    # on non command i.e message - respuesta the message on Telegram
    application.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, respuesta))

    # Run the bot until the user presses Ctrl-C
    application.run_polling(allowed_updates=Update.ALL_TYPES)


if __name__ == "__main__":
    main()

[Base_bot.csv](https://github.com/user-attachments/files/16065975/Base_bot.csv)
