mastercoin-tools
================

## What is it? ##
* Package of mastercoin related tools.
* The intention is to help finalizing the mastercoin spec, and enable easy further coding.

## Based on what? ##
* The code uses the package sx, which is libbitcoin based.
* bugfixes for sx as well as new features like BIP11 and get-pubkey which are needed
in mastercoin-tools got merged:
https://github.com/spesmilo/sx/commits?author=grazcoin


## Aim of this package ##
* Live update - direct "realtime" interaction with the bitcoin network (using sx).
* Light node - run without a need for a database.
* Alternative code base - use libbitcoin instead of "satoshi client".
* Sample cool and mobile friendly web UI
* Offline usage option
* Send transaction directly (not "Advisor")
* Support for offline wallets
* Generate parsed data snapshots for download
* API (json)

## Let's see something ##

### Already implemented ###

* Cool web UI. Mobile phones and Tablets friendly. http://masterchain.info
* Seperation per currency/asset (with future support for price trend on distributed market)
* Fully distributed
 * Local installation/Offline/Static usage - just download and use - see https://raw.github.com/grazcoin/mastercoin-tools/master/INSTALL
 * Easy update of a new installation (no need for a database):
  * Using download snapshot of latest parsed data from http://masterchain.info/downloads/ or
  * Run update directly from a remote bitcoin node (obelisk) using https://github.com/grazcoin/mastercoin-tools/blob/master/msc_parse.py
* Parse mastercoin tx by listening to 1ExoDus tx streams and validate balances.
* Simple send script that interracts directly with the bitcoin network (obelisk server).
 * Basic mode (original paper) and simple multisig (see below) are supported.
* Built in API:
 * Transactions: http://masterchain.info/tx/$TX_ID.json
 * Addresses: http://masterchain.info/addr/$ADDR.json
 * Latest transactions: http://masterchain.info/general/$CURRENCY_$PAGE_NUMBER.json
* Exodus 10% bonus calculated at http://masterchain.info/Address.html?addr=exodus

### Next steps ###

* Add support for multiple assets and distributed exchange.

### Mastercoin Improvement Proposal (MIP1) ###
```
simple multisig:

output 1:   dust to 1EXoDus

output 2:   all the change to BIP11 1-of-2:

            pubkey1 is the one of the sender (redeemable)

            pubkey2 is recipientHex+dataHex+padding
```

### Example transaction ###

On the blockchain:
https://blockchain.info/tx/aa64fd6088532156a37670e6cbd175c74bb101f1406517613a1a0ae6bc02fb02

On Masterchain:
http://masterchain.info/Transaction.html?tx=aa64fd6088532156a37670e6cbd175c74bb101f1406517613a1a0ae6bc02fb02


### Parsing usage examples ###
```
$ python msc_parse.py -h
Usage: msc_parse.py [options]

Options:
  -h, --help            show this help message and exit
  -d, --debug           turn debug mode on
  -t SINGLE_TX, --transaction=SINGLE_TX
                        hash of a specific tx to parse
  -s STARTING_BLOCK_HEIGHT, --start-block=STARTING_BLOCK_HEIGHT
                        start the parsing at a specific block height (default
                        is last)
  -a, --archive-parsed-data
                        archive the parsed data of tx addr and general for
                        others to download
```

```
$ python msc_parse.py -t aa64fd6088532156a37670e6cbd175c74bb101f1406517613a1a0ae6bc02fb02
[I] main: {'currency_type_str': 'Mastercoin', 'transaction_type_str': 'Simple send', 'currencyId': '00000001', 'transaction_method_str': 'multisig_simple', 'recipientAddress': '17RVTF3vJzsuaGh7a94DFkg4msJ7FcBYgX', 'padding': '000000', 'amount': '0000000002faf080', 'changeAddress': '182osbPxCo88oaSX4ReJwUr9uAcchmJVaL', 'formatted_amount': '0.50000000', 'baseCoin': '00', 'dataSequenceNum': '45', 'transactionType': '00000000'}
$
$ python msc_parse.py -t 298a6af50089184f7b434c700f83f390d5dfdd5dac10b39b95f99036a5c66df7
[I] main: {'currency_type_str': 'Test Mastercoin', 'transaction_type_str': 'Simple send', 'currencyId': '00000002', 'transaction_method_str': 'multisig_simple', 'recipientAddress': '17RVTF3vJzsuaGh7a94DFkg4msJ7FcBYgX', 'padding': '000000', 'amount': '0000000000000003', 'changeAddress': '182osbPxCo88oaSX4ReJwUr9uAcchmJVaL', 'formatted_amount': '0.00000003', 'baseCoin': '00', 'dataSequenceNum': '45', 'transactionType': '00000000'}
$
```

```
$ python msc_validate.py -h
Usage: msc_validate.py [options]

Options:
  -h, --help   show this help message and exit
  -d, --debug  turn debug mode on
```

### Sending usage examples ###
```
$ python msc_send.py -h
Usage: msc_send.py [options]

Options:
    -h, --help            show this help message and exit
    -m TX_METHOD, --transaction-method=TX_METHOD
                          basic or multisig
    -c CURRENCY_ID, --currency-id=CURRENCY_ID
                          1 for Mastercoin, 2 for Test Mastercoin
    -a AMOUNT, --amount=AMOUNT
                          amount of coins
    -x FEE, --fee=FEE     fee for transaction
    -r RECIPIENT_ADDRESS, --recipient=RECIPIENT_ADDRESS
                          recipient address
    -f FROM_ADDRESS, --from=FROM_ADDRESS
                          from address or pubkey
    -p PRIV_KEY, --private-key=PRIV_KEY
                          private key for signing the tx (overrides from
                          address)
    -k, --key-prompt      prompt for hidden private key for signing the tx
                          (overrides from address)
    -s HOST_PORT, --send-tx=HOST_PORT
                          transmit tx to specific bitcoin node HOST:PORT
    -b, --broadcast-tx    broadcast tx to bitcoin network
    -d, --debug           turn debug mode on
```

```
$ python msc_send.py --transaction-method=multisig --currency-id=2 --amount=0.00000003 --recipient=17RVTF3vJzsuaGh7a94DFkg4msJ7FcBYgX --key-prompt --fee=0.005000000
[I] main: Using settings: {'broadcast': False, 'recipient_address': '17RVTF3vJzsuaGh7a94DFkg4msJ7FcBYgX', 'fee': '0.005000000', 'from_address': 'UNKNOWN', 'key_prompt': True, 'host_port': None, 'currency_id': 2, 'amount': '0.00000003', 'debug_mode': False, 'priv_key': None, 'tx_method': 'multisig'}
Enter your private key:
[I] main: Private key was entered
Added input ca234cf9e041a942d8a56bb3449f15f1176beb3291af60a35392b72b2080fe1f:7
Added output sending 6000 Satoshis to 1EXoDusjGwvnjZUyKkxZ4UHEf77z6A5S4P.
Added output sending 4494000 Satoshis to 1 [ 041f204911ec19cb5b7b10dd87ccf6a52552466d14356212e881288512eeff8e2084ddff9997fdfb22fae6b09a255e3937a7890491ab5106ce7912bc253e430887 ] [ 0046727d1b3d6847f9ed344561a315f54b801edf637cad93d000450000000000000002000000000000000300000000000000000000000000000000000000000000 ] 2 checkmultisig.
[I] sign: signing tx
[I] main: validating tx: Status: Success
[I] main: SIGNED tx (multisig) of 0.00000003 Test Mastercoin to 17RVTF3vJzsuaGh7a94DFkg4msJ7FcBYgX signed by 182osbPxCo88oaSX4ReJwUr9uAcchmJVaL
[I] parse_multisig_simple: 1 [ 041f204911ec19cb5b7b10dd87ccf6a52552466d14356212e881288512eeff8e2084ddff9997fdfb22fae6b09a255e3937a7890491ab5106ce7912bc253e430887 ] [ 0046727d1b3d6847f9ed344561a315f54b801edf637cad93d000450000000000000002000000000000000300000000000000000000000000000000000000000000 ] 2 checkmultisig
[I] parse_test: {'currency_type_str': 'Test Mastercoin', 'transaction_type_str': 'Simple send', 'currencyId': '00000002', 'transaction_method_str': 'multisig_simple', 'recipientAddress': '17RVTF3vJzsuaGh7a94DFkg4msJ7FcBYgX', 'padding': '000000', 'amount': '0000000000000003', 'changeAddress': '182osbPxCo88oaSX4ReJwUr9uAcchmJVaL', 'formatted_amount': '0.00000003', 'baseCoin': '00', 'dataSequenceNum': '45', 'transactionType': '00000000'}
[I] main: please send using "sx broadcast-tx signed_tx.tx"
```


### Bootstrap related examples ###
To get all mastercoins sells during exodus bootstrap in csv format:
```
python msc_bootstrap.py > outputs/bootstrap.log
```

To get total amount of mastercoins for each address:
```
python bootstrap_msc_per_address.py > outputs/msc_per_address.csv
```

To get the whole bootstrap story (for fun and debug), check:
```
python msc_bootstrap.py story > outputs/bootstrap_story.log
```

The outputs of those scripts are available under outputs directory. 
Notes for redeeming the Escrow tx, are in NOTES.


enjoy!

BTC/Mastercoins Tips Jar:
* https://blockchain.info/address/182osbPxCo88oaSX4ReJwUr9uAcchmJVaL
* http://masterchain.info/Address.html?addr=182osbPxCo88oaSX4ReJwUr9uAcchmJVaL&currency=MSC



