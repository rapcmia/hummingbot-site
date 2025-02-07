## Installation

### Source: ModuleNotFoundError

```
ModuleNotFoundError: No module named 'hummingbot.market.market_base'
root - ERROR - No module named
‘hummingbot.strategy.pure_market_making.inventory_skew_single_size_sizing_delegate’
(See log file for stack trace dump)
```

Solution 1: exit Hummingbot to compile and restart using these commands:

```
conda activate hummingbot
./compile
bin/hummingbot.py
```

Solution 2: make sure you have conda section in ~/.bashrc. Run conda init if it is not there. Explanation: if you have custom PATH defined in ~/.bashrc, supplied scripts (./compile etc) may pick wrong python binary, causing different errors.

### Source: SyntaxError invalid SyntaxError

```
File "bin/hummingbot.py", line 40
  def detect_available_port(starting_port: int) -> int:
                                           ^
SyntaxError: invalid syntax
```

### Source: conda command not found

```
$ conda
-bash: conda: command not found
```

If you have just installed conda, close terminal and reopen a new terminal to update the command line's program registry.

If you use `zshrc` or another shell other than `bash`, see install dependencies (<https://hummingbot.org/installation/source/#xcode-command-line-tools>)

### Docker: Package 'docker.io' has no installation candidate

![Hummingbot installed](/assets/img/package-docker-io.png)

Install Docker using get.docker.com script as an alternative. Install curl tool then download and run get.docker.com script.

```
apt-get install curl
curl -sSL https://get.docker.com/ | sh

```

Allow docker commands without requiring sudo prefix (optional).

```
sudo usermod -a -G docker $USER
```

### Docker: Permission denied after Docker installation

```
docker: Got permission denied while trying to connect to the Docker daemon socket at
unix:///var/run/docker.sock: Post
http://%2Fvar%2Frun%2Fdocker.sock/v1.39/containers/create?name=hummingbot_instance:
dial unix /var/run/docker.sock: connect: permission denied.
```

Exit from your virtual machine and restart.

### Docker: How to stop active container?

You need to run `docker ps -a` to get the list of containers available. Then you can do the following,

1. Locate the container id of the bot you want to stop
2. Run `docker container stop [container-id]`

![Docker Container Stop](/assets/img/docker-container-stop.PNG)

!!! note
    Please be advised this will force close the bot from running on Docker. This means that this does not stop the outstanding orders from the HB client. The commands `stop` and `exit` is the right way to cancel orders.

## Operation

### AMM Arbitrage strategy is not working

Some few important things to check:

- Assets in both base and quote
- Make sure gateway is running
- Some ETH for gas
- Installed Hummingbot-gateway and configured it to mainnet/testnet depending on where you are using it.
- Connected Ethereum wallet and put in the right ethereum node corresponding to what you have put in installing your gateway.

### Why is my bot not creating orders on Perpetual Market-Making?

Check if you have an open position on Binance, it'll stop creating a new set of orders until your current position is closed. To learn more about perpetual market making, click [here](/strategies/perpetual-market-making).

### I have my own strategy, can I make the bot execute it?

Hummingbot is an open-source application that you can create your own custom scripts and build strategy. Guidelines has been created so our community can have their way to improve or add features.

You can check our [Discord](https://discord.gg/hummingbot) and discuss in our `#developer-chat` channel where you can share your ideas or ask questions about how to implement your strategy. This link would also help you more on [Developing Strategies](/developers/strategies/tutorial).

You can also check out our **new** `scripts` feature which allows you to create a complete strategy using just a single Python file. Check it out [here](/scripts/index)

### Orders are not refreshing according to order refresh time

Make sure to set your `order_refresh_tolerance_pct` to -1 if you are not using the parameter.

When using the parameter `order_refresh_tolerance`, orders do not refresh according to order refresh time if it doesn't exceed the percentage (%) threshold you set under `order_refresh_tolerance_pct`.

### MAC Mismatch error

```
Hummingbot.core.utils.async_utils - ERROR - Unhandled error in background task: MAC mismatch Traceback (most recent call last):
File "/home/ubuntu/hummingbot/hummingbot/core/utils/async_utils.py", line 9, in safe_wrapper return await c
File "/home/ubuntu/hummingbot/hummingbot/core/utils/async_call_scheduler.py", line 128, in call_async return await self.schedule_async_call coro, timeout_seconds, app_warning_msg=app_warning_msg)
File "/home/ubuntu/hummingbot/hummingbot/core/utils/async_call_scheduler.py", line 117, in schedule_async_call return await fut
File "/home/ubuntu/hummingbot/hummingbot/core/utils/async_call_scheduler.py", line 80, in _coro_scheduler fut.set_result(await coro)
File "/home/ubuntu/miniconda3/envs/hummingbot/lib/python3.8/concurrent/futures/thread.py", line 57, in run result = self.fn(*self.args, **self.kwargs)
File "/home/ubuntu/hummingbot/hummingbot/client/config/security.py", line 88, in decrypt_all cls.decrypt_file(file)
File "/home/ubuntu/hummingbot/hummingbot/client/config/security.py", line 73, in decrypt_file cls._secure_configs[key_name] = decrypt_file(file_path, Security.password)
File "/home/ubuntu/hummingbot/hummingbot/client/config/config_crypt.py", line 67, in decrypt_file secured_value = Account.decrypt(encrypted, password)
File "/home/ubuntu/miniconda3/envs/hummingbot/lib/python3.8/site-packages/eth_account/account.py", line 134, in decrypt return HexBytes(decode_keyfile_json(keyfile, password_bytes))
File "/home/ubuntu/miniconda3/envs/hummingbot/lib/python3.8/site-packages/eth_keyfile/keyfile.py", line 49, in decode_keyfile_json return _decode_keyfile_json_v3(keyfile_json, password)
File "/home/ubuntu/miniconda3/envs/hummingbot/lib/python3.8/site-packages/eth_keyfile/keyfile.py", line 170, in _decode_keyfile_json_v3 raise ValueError("MAC mismatch")
ValueError: MAC mismatch
```

This error is usually caused by having multiple encrypted keys with different passwords in the same config folder. For example:

```
Instance1                       Instance2
Password  : 1234                Password  : 5678
API key/s : Binance             API key/s : Bittrex, Coinbase Pro,
                                            Eterbase, Kraken, Huobi
```

Copying encrypted Binance key file from Instance1 to Instance2 will result to this error. To fix this:

1. Delete just the `encrypted_binance_api/secret_key.json` from Instance2's conf folder
2. Restart Hummingbot and password 5678 remains unchanged
3. Run `connect binance` and add the API keys - this will encrypt it with 5678 password and sync it with the rest of the API keys

### Timestamp for this request is outside of the recvWindow

```
binance.exceptions.BinanceAPIException: APIError(code=-1021): Timestamp for this request is outside of the recvWindow.
```

Timestamp errors in logs happen when the Binance clock gets de-synced from time to time as they can drift apart for a number of reasons. Hummingbot should safely recover from this and continue running normally.

### Too much request weight used; IP banned

**Sample log error message**:

```
binance.exceptions.BinanceAPIException: APIError(code=-1003): Way too much request weight used; IP banned until 1573987680818. Please use the websocket for live updates to avoid bans
```

This error occurs when the Binance API rate limit is reached. Causes include:

Using multiple order mode with 3+ orders per side
High order refresh rate
Running multiple instances of Hummingbot
Weight/Request error in logs happens when it encounters a warning or error and Hummingbot repeatedly sends the request (fetching status updates, placing/canceling orders, etc.) which resulted in getting banned. This should be lifted after a couple of hours or up to a maximum of 24 hours.

## Kraken 0 Balance error

```
Failed connections:                                                                                      |
    kraken: {'error': {'error': []}}

10:12:24 - kraken_market - Error received from https://api.kraken.com/0/private/Balance. Response is {'error': []}.
```

This error occurs when Kraken account currently has no funds on the exchange. Fund your account to fix the error. For more info visit this [here](https://support.kraken.com/hc/en-us/articles/360001491786-API-Error-Codes).

## Gateway

### async_utils - Unhandled error in background task: `chain_type`

![Gateway error](/gateway-03.png)

![Gateway error](/gateway-02.png)

If you get the above errors after updating the Hummingbot client to v1.16.0, make sure to update the Gateway to the latest version as well. After updating, run the [gateway-setup](https://github.com/hummingbot/gateway/blob/main/gateway-setup.sh) script to regenerate your configs then re-run the `gateway connect ...` command in Hummingbot (ex. gateway connect uniswap)

### TypeError: Password was given but private key is not encrypted

![Gateway error](/gateway-01.png)

Make sure to set at least one character as your Hummingbot login password. Having a blank password causes the Gateway error message. For instructions on how to reset your password check this [link](/operation/password)

### Error after running generate_certs command

![Hummingbot installed](/assets/img/running-log.png)

Add permission to the cert folder or to your hummingbot instance folder: `sudo chmod a+rw <[instance_folder]` or `[certs_folder]>/\*`

### Why is my bot not placing orders?

Fetch your bot status by running `status` or `[Ctrl + S]`:

- Are there any warnings that may prevent the bot from starting?
- Is your `order_amount` parameter larger than the exchange minimum order size requirement?
- If the user doesn't have enough balance to meet the set order amount, the bot will still try to create an order with a smaller order amount size provided that it still meets the exchange minimum requirement.
- Is your `inventory_skew_enabled` parameter enabled? Since this parameter adjusts order sizes, one side may be too low or too high.

### Change the time or timezone of Hummingbot

Hummingbot follows the same date/time and timezone on the machine where it is intalled. Below are some steps you can follow to change the timezone depending on the operating system and installation type.

**Docker**

While docker `$instance_name` is running in the background, type in the command line.

Manual

```
# 1) Run this command
docker exec -u 0 -it instance_name bash

# 2) Install tzdata for the instance/container
apt-get update && apt-get install -y tzdata

# 3) Run this command to change the docker timezone
dpkg-reconfigure tzdata
```

Configure geographic location and timezone by inputting the corresponding number, see example below:

![](/assets/img/time-zone.PNG)

Restart your docker and start your Hummingbot again to apply changes.

**Windows**

You can change the timezone on a Windows computer by doing the following:

1. Press **Win + R** shortcut to open the Run dialog box

2. Enter **timedate.cpl** to open Date and Time settings

3. Click **Change time zone**

![](/assets/img/win-time.PNG)

You can also follow these steps in the Windows Support article: [How to set your time and timezone](https://support.microsoft.com/en-us/windows/how-to-set-your-time-and-time-zone-dfaa7122-479f-5b98-2a7b-fa0b6e01b261)
