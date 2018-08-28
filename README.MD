# EOS Yield Contract

This contract demostrates how an EOS contract can be made immutable in a given period of time, without losing mutability forever. Such a mechanism guarantees immutability when trustlessness is necessary, with code changes possible with prior notice to users.

## Clone and Compile

To clone the repository and compile the contract into binary:

    $ git clone https://github.com/xJonathanLEI/eosyield.git
    $ cd eosyield
    $ eosiocpp -o eosyield.wast eosyield.cpp
    $ eosiocpp -g eosyield.abi eosyield.cpp

Three files: `eosyield.wast`, `eosyield.wasm` and `eosyield.abi` will be generated after compilation succeeds.

## Contract Setup

To test the contract, first set up a contract account, say `yield`:

    $ cleos system newaccount --buy-ram "1000.0000 SYS" --stake-net "100.0000 SYS" --stake-cpu "100.0000 SYS" eosio yield YOUR_PUBLIC_KEY;

Publish the contract:

    $ cleos set contract yield /path/to/contract/folder;

Give account `owner` permission to `eosio.code` permission so that the contract itself can modify its own permission settings:

    $ cleos set account permission yield owner '{"threshold": 1,"keys": [{"key": "YOUR_PUBLIC_KEY","weight": 1}],"accounts": [{"permission":{"actor":"yield","permission":"eosio.code"},"weight":1}]}' -p yield@owner;

Set the owner to be your account (`jonathan` here):

    $ cleos push action yield setowner '["jonathan"]' -p yield@owner;

To yield control on the contract:

    $ cleos push action yield yieldcontrol '[60]' -p jonathan;

If you check permission now, you would see the contract code would be the sole owner of itself:

    $ cleos get account yield;

Check the yield info from database:

    $ cleos get table yield yield yieldinfo;

To extend the yield period:

    $ cleos push action yield extend '[120]' -p jonathan;

To regain control after the expiration is reached:

    $ cleos push action yield regain '' -p jonathan;

## License

This EOS contract is licensed under the MIT license.
