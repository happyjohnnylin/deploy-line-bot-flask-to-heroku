# Deploy LINE bot (Flask version) to Heroku

Prerequisites:
-----------------------------------------
1. Created a channel for your bot in LINE Developer [Console](https://developers.line.me/console/)
2. A [Heroku account](https://www.heroku.com) (you can create one for free)
3. [Install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) on your terminal
4. [Install Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)


Step 1: Login your Heroku account on terminal
---------------
    $ heroku login
    $ Email: {YOUR_SIGNED_UP_EMAIL}
    $ Password: {YOUR_PASSWORD}
    
    
Step 2:Clone the repository
---------------
Use Git to clone this repository or directly download to your local machine.

    $ git clone {LINK_TO_THIS_REPOSITORY}
 
 
Step 3: Modify app.py
--------
Change the **channel secret** and **channel access token** provided from the [console](https://developers.line.me/console/)
```
    from flask import Flask, request, abort

    from linebot import (
        LineBotApi, WebhookHandler
    )
    from linebot.exceptions import (
        InvalidSignatureError
    )
    from linebot.models import (
        MessageEvent, TextMessage, TextSendMessage,
    )

    app = Flask(__name__)

    line_bot_api = LineBotApi('YOUR_CHANNEL_ACCESS_TOKEN')
    handler = WebhookHandler('YOUR_CHANNEL_SECRET')


    @app.route("/callback", methods=['POST'])
    def callback():
        # get X-Line-Signature header value
        signature = request.headers['X-Line-Signature']

        # get request body as text
        body = request.get_data(as_text=True)
        app.logger.info("Request body: " + body)

        # handle webhook body
        try:
            handler.handle(body, signature)
        except InvalidSignatureError:
            abort(400)

        return 'OK'


    @handler.add(MessageEvent, message=TextMessage)
    def handle_message(event):
        line_bot_api.reply_message(
            event.reply_token,
            TextSendMessage(text=event.message.text))


    if __name__ == "__main__":
        app.run()
```    
  
  
Step 4: Create a new Heroku app
-------------------
Create a new Heroku app from the Heroku dashboard and copy the app name.


Step 5: Add a remote to your local repository
----------------
Use the git remote command to add a remote connection from your local repository to your Heroku app.
Note: {HEROKU_APP_NAME} is the app name from step 4.

    $ heroku git:remote -a {HEROKU_APP_NAME}


Step 6: Deploy the repository to Heroku using Git 
---------------
1. Get into the folder *deploy-line-bot-flask-to-heroku* or other places where the repository is located.
    
    $ cd deploy-line-bot-flask-to-heroku
    
2. Add the repository to git, commit it, and push to heroku master.
   
    $ git init  
    $ git add .
    $ git commit -m "First commit"
    $ git push heroku master
    
    
Step 7:Enter the webhook URL in the [console](https://developers.line.me/console/)
-----------------------
Enter the webhook URL in the console using the following URL format: https://{HEROKU_APP_NAME}.herokuapp.com/callback


**You can test your chat bot right now!**



About the LINE Messaging API
----------------------------
See the official LINE Messaging API documentation for more information.

English: https://developers.line.me/en/docs/messaging-api/reference/

Japanese: https://developers.line.me/ja/docs/messaging-api/reference/
