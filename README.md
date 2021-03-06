# optimal-buy-gdax
Scheduled buying of BTC, ETH, and LTC from GDAX optimally! Be your own index/hedge fund, and stop paying management fees to someone else!

![crypto](crypto.gif)

# What is this?

This is a Python script you can use to automatically buy Bitcoin, Ethereum,
and Litecoin using the GDAX API. It buys these 3 currencies, weighted by market
cap (as reported by [coinmarketcap.com](https://coinmarketcap.com/)), using a form of [dollar cost averaging](https://www.bogleheads.org/wiki/Dollar_cost_averaging). according
to the following logic:

1. Check current balances of USD, BTC, ETH, and LTC
1. If the USD balance is above $100, buy BTC, ETH, and LTC weighted by market cap
1. If there's enough USD available, place 5 discounted limit orders at the current price minus 0.5% up to 0.55%,
each order with 1/5th of the remaining amount to buy for each coin
1. If there isn't enough USD available, place 1 buy order at 0.5% off the current price
1. If the USD account balance is below $100, withdraw coins to desired addresses

You can also use the same script to schedule deposits from your bank account
periodically, such as when you're paid.

Ideally, this script would help to make sure that when we dip—

![dip](buy-the-dip.gif)

**we buy**.

# USE AT OWN RISK

Duh. Not my fault if you lose everything.

Unless you place **absolute trust** in me, some guy from the Internet, I suggest you clone the repo and build your own container to protect yourself from any sort of funny business.

# How do I use it?

1. Get yourself a hardware wallet, such as a [Ledger](https://www.ledgerwallet.com/) or [TREZOR](https://trezor.io/).
1. Set up a GDAX account, and link your bank account
1. Create the necessary API credentials for GDAX, with permissions to
manage funds, withdraw without 2FA, and trade
1. Determine the payment_method_id value by using the [GDAX API](https://docs.gdax.com/#payment-methods) (you can use your browser's developer toolbar, [here's a quick video showing how](https://youtu.be/NmSEBGbn7Mc))
1. Get a machine somewhere (GCE, EC2, Digital Ocean) with Docker and systemd
1. Copy systemd files over:

        $ sudo cp optimal-buy-gdax-*.{service,timer} /etc/systemd/system
1. Edit [`/etc/systemd/system/optimal-buy-gdax-buy.service`](optimal-buy-gdax-buy.service),
[`/etc/systemd/system/optimal-buy-gdax-buy.timer`](optimal-buy-gdax-buy.timer),
[`/etc/systemd/system/optimal-buy-gdax-deposit.service`](optimal-buy-gdax-deposit.service), and
[`/etc/systemd/system/optimal-buy-gdax-deposit.timer`](optimal-buy-gdax-deposit.timer) to your liking. Make sure you:

    * Change the BTC, ETH, and LTC deposit addresses to deposit the coins into your wallet (use a Ledger or TREZOR)
    * Put the correct API keys in
    * Check the deposit amount (start with something small, like $150, to make sure it actually works first)
    * Check the timer dates

1. Enable the systemd units:

        $ sudo systemctl enable optimal-buy-gdax-buy.service
        $ sudo systemctl enable optimal-buy-gdax-buy.timer
        $ sudo systemctl enable optimal-buy-gdax-deposit.service
        $ sudo systemctl enable optimal-buy-gdax-deposit.timer

1. Start the systemd timers:
        $ sudo systemctl start optimal-buy-gdax-buy.timer
        $ sudo systemctl start optimal-buy-gdax-deposit.timer

1. Enjoy!

# Caveats/limitations

* Currently it only supports USD
* Some values are hardcoded, but if you want to change that feel free to send a
PR!
* If you try to trade manually or using some other bot at the same time,
you're probably going to have a bad time
* You might have a few dollars (<$100) sitting in your account at all times, even when all orders have been filled

# Tip jar

If you got some value out of this, please send some kudos my way:

* BTC: 3EEAE1oKEMnmHGU5Qxibv9mBQyNnes8j8N
* LTC: 3MxmLzTf4sPsFBGYUnX9MMMbTMeaUSox46
