# telepot - Python framework for Telegram Bot API

## Original author Nikola no longer maintaining this library. KH is maintaining it for the support of new telegram UI.

### [Introduction »](http://telepot.readthedocs.io/en/latest/)
### [Reference »](http://telepot.readthedocs.io/en/latest/reference.html)
### [Examples »](https://github.com/nickoala/telepot/tree/master/examples)
### [Changelog »](https://github.com/nickoala/telepot/blob/master/CHANGELOG.md)

The new telegram UI added non-standard way of stopping the bot, causing the bot server malfunction in unforeseen circumstance.
This triggered by the frontend users which stop the interaction with the bot via the new UI.

![image](https://user-images.githubusercontent.com/32192638/123756394-749a8880-d8ef-11eb-9acc-668d367d9fce.png)

As a result, telepot library turned into an infinite loop receiving these kind of non-standard message as exception errors.
The application calling telepot library unable to pickup subsequent incoming messages, therefore bot users mistaken that the bot is dead.

Resolution
- Code change to recognised this kind of special message as known errors, it proceeds to read the next possible incoming message without being "hanged".
- The application developer calling this telepot library has to clear the history manually. The workaround to write a function to "clear history" whenever the application restarts the telegram bot.

Example function to clear history:
```python
    def clear_updates(Token):
        api_url = f"https://api.telegram.org/bot{Token}/getUpdates"
        response = requests.get(api_url)
        if response.status_code==200:
            result = response.content.decode('utf-8')
            if len(result)>0:
                data = json.loads(result)
                result = [ x['update_id'] for x in data['result'] if 'update_id' in list(x) ]
                if len(result)>0:
                    update_id = result[-1] + 1
                    api_url = f"https://api.telegram.org/bot{Token}/getUpdates?offset={update_id}"
                    response = requests.get(api_url)
        return
```
If someone click "stop the bot" from the frontend UI again, the bot will not hang anymore when this new library in used.


