# line-bot-sdk-python-on-heroku

===================

|Build Status| |PyPI version| |Documentation Status|

SDK of the LINE Messaging API for Python.

About the LINE Messaging API
----------------------------

See the official API documentation for more information.

English: https://developers.line.me/en/docs/messaging-api/reference/

Japanese: https://developers.line.me/ja/docs/messaging-api/reference/


Synopsis(app.py)
--------

Usage:

.. code:: python

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


.. |Build Status| image:: https://travis-ci.org/line/line-bot-sdk-python.svg?branch=master
   :target: https://travis-ci.org/line/line-bot-sdk-python
.. |PyPI version| image:: https://badge.fury.io/py/line-bot-sdk.svg
   :target: https://badge.fury.io/py/line-bot-sdk
.. |Documentation Status| image:: https://readthedocs.org/projects/line-bot-sdk-python/badge/?version=latest
   :target: http://line-bot-sdk-python.readthedocs.io/en/latest/?badge=latest
