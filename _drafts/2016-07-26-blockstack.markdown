---
layout: "post"
title: "Blockstack in Practice"
author: Mark Hennessy, Stefan van den Oord, Mark Spanbroek
---
### Blockstack CLI usage overview

It was straightforward to install Blockstack CLI in order to test and use Blockstack. In order to actually make use of blockstack it was necessary to purchase some bitcoin. This was done by visiting a bitcoin exchange, purchasing some bitcoin and sending a required amount to a blockstack address generated in the following way:

	$ blockstack price mahennessy.id

	{
	    "name_price": 25000,
	    "preorder_tx_fee": 17205,
	    "register_tx_fee": 15865,
	    "total_estimated_cost": 96502,
	    "update_tx_fee": 38432
	}

	$ blockstack register mahennessy.id

	Registering mahennessy.id will cost 0.00212416 BTC. Continue? (y/n): y
	Address 14geS2eP46mKCvKEu2rjoY7g5ou7wkU29Z doesn't have enough balance (need 212416).
	Marks-MacBook-Pro-2:.blockstack mark.hennessy$ blockstack deposit
	{
	    "address": "14geS2eP46mKCvKEu2rjoY7g5ou7wkU29Z",
	    "message": "Send bitcoins to the address specified."
	}

Once the block with your transaction is confirmed then you can check your balance at your Blockstack address. (The balance below is in terms of BTC):

	$ blockstack balance

	{
	    "addresses": [
	        {
	            "address": "14geS2eP46mKCvKEu2rjoY7g5ou7wkU29Z",
	            "balance": 114698
	        }
	    ],
	    "total_balance": 114698.0
	}

	$ blockstack register mahennessy.id

	Registering mahennessy.id will cost 0.00087094 BTC. Continue? (y/n): y

	{
	    "message": "The name has been queued up for registration and will take a few hours to go through. You can check on the status at any time by running 'blockstack info'.",
	    "success": true,
	    "transaction_hash": "c7ec9f0312751d77591fae93f106fa086dab09f89e50159d6e4724d8c7630f16"
	}

The following command will let you know in more detail the status of your name registration order. It will stay in this state until 6 confirmations are completed in the bitcoin blockchain (you may notice some hashes and addresses are obfuscated from here on):

	$ blockstack info

	{
	    "advanced_mode": false,
	    "cli_version": "0.0.13.7",
	    "consensus_hash": "XXXXXXXX",
	    "last_block_processed": 421828,
	    "last_block_seen": 421834,
	    "queue": {
	        "preorder": [
	            {
	                "confirmations": 4,
	                "name": "mahennessy.id",
	                "tx_hash": "c7ec9f0312751d77591fae93f106fa086dab09f89e50159d6e4724d8c7630f16"
	            }
	        ]
	    },
	    "server_alive": true,
	    "server_host": "node.blockstack.org",
	    "server_port": "6264",
	    "server_version": "0.0.13.1"
	}

Until the name registration is confirmed in the blockchain you will get the following response when you check what names are owned by you:

	$ blockstack names
	{
	    "addresses": [
	        {
	            "address": "YYYYYYYY",
	            "names_owned": []
	        }
	    ],
	    "names_owned": []
	}

Once the requested name registration is confirmed you can run the following command to see it:

	$ blockstack names

	{
	    "addresses": [
	        {
	            "address": "YYYYYYYY",
	            "names_owned": [
	                "mahennessy.id"
	            ]
	        }
	    ],
	    "names_owned": [
	        "mahennessy.id"
	    ]
	}

A more detailed lookup can be performed using the following command:

	$ blockstack lookup mahennessy.id

	{
	    "profile": {
	        "@type": "Person",
	        "accounts": []
	    },
	    "zonefile": {
	        "$origin": "mahennessy.id",
	        "$ttl": 3600,
	        "txt": [
	            {
	                "name": "pubkey",
	                "txt": "pubkey:data:0217c939d7b1a96d1d8053316069961d2bc5beaff61ac7e153be4a09da08d2f051"
	            }
	        ],
	        "uri": [
	            {
	                "name": "\_file",
	                "priority": 10,
	                "target": "file:///Users/mark.hennessy/.blockstack/storage-disk/mutable/mahennessy.id",
	                "weight": 1
	            },
	            {
	                "name": "\_https.\_tcp",
	                "priority": 10,
	                "target": "https://blockstack.s3.amazonaws.com/mahennessy.id",
	                "weight": 1
	            },
	            {
	                "name": "\_http.\_tcp",
	                "priority": 10,
	                "target": "http://node.blockstack.org:6264/RPC2#mahennessy.id",
	                "weight": 1
	            },
	            {
	                "name": "\_dht.\_udp",
	                "priority": 10,
	                "target": "dht+udp://6307c0f18030f4cc96fe4f91946efc68f5bac9ce",
	                "weight": 1
	            }
	        ]
	    }
	}

To get more information about where the name registration sits in the underlying blockchain and including the expiration date of the registered name:

	$ blockstack whois mahennessy.id

	{
	    "approx_expiration_date": "2017 Aug 09 00:42:31 UTC",
	    "block_preordered_at": 421831,
	    "block_renewed_at": 421842,
	    "expire_block": 474437,
	    "has_zonefile": true,
	    "last_transaction_height": 421856,
	    "last_transaction_id": "af29447dfd52bce8243450485ea2f02145f0c6456a0b8a6b99a26e3859a5a2b1",
	    "owner_address": "1NH3QdobQnmyeqospDd2bRqWNvpbJ8i8eJ",
	    "owner_script": "76a914e9634e782b469683fc7e9a7e3143fd719185980b88ac",
	    "zonefile_hash": "ad0135dcd269c8a52c40ac5556ee15d2d829ac74"
	}

It is also possible to update, migrate and transfer a name based on blockstack commandline help:

	$ blockstack
	usage: blockstack [-h]
	                  {balance,configure,deposit,import,info,lookup,migrate,names,ping,price,register,renew,revoke,set_advanced_mode,transfer,update,whois}
	                  ...

	Blockstack cli version 0.0.13.7

	positional arguments:
	  {balance,configure,deposit,import,info,lookup,migrate,names,ping,price,register,renew,revoke,set_advanced_mode,transfer,update,whois}
	    balance             Get the account balance
	    configure           Interactively configure the client
	    deposit             Display the address with which to receive bitcoins
	    import              Display the address with which to receive names
	    info                Get details about pending name commands
	    lookup              Get the zone file and profile for a particular name
	    migrate             Migrate a profile to the latest profile format
	    names               Display the names owned by local addresses
	    ping                Check server status and get server details
	    price               Get the price of a name
	    register            Register a name
	    renew               Renew a name
	    revoke              Revoke a name
	    set_advanced_mode   Enable advanced commands
	    transfer            Transfer a name to a new address
	    update              Set the zone file for a name
	    whois               Look up the blockchain info for a name

	optional arguments:
	  -h, --help            show this help message and exit
	{}

It is also possible to enable more advanced commands that allow data associated with the name to be added, updated and made public or private:

	$ blockstack set_advanced_mode
	usage: blockstack set_advanced_mode [-h] status
	blockstack set_advanced_mode: error: too few arguments

	Interactive prompt engaged.  Press Ctrl+C to quit
	Help for 'set_advanced_mode': Enable advanced commands

	On or Off. ('status'): On  
	{
	    "status": true
	}

Check the newly enabled commands:

	$ blockstack
	usage: blockstack [-h]
	                  {balance,configure,deposit,import,info,lookup,migrate,names,ping,price,register,renew,revoke,set_advanced_mode,transfer,update,whois,consensus,delete_account,delete_immutable,delete_mutable,get_account,get_all_names,get_immutable,get_mutable,get_name_blockchain_history,get_name_blockchain_record,get_name_zonefile,get_nameops_at,get_names_in_namespace,get_names_owned_by_address,get_namespace_blockchain_record,get_namespace_cost,import_wallet,list_accounts,list_immutable_data_history,list_update_history,list_zonefile_history,lookup_snv,name_import,namespace_preorder,namespace_ready,namespace_reveal,put_account,put_immutable,put_mutable,rpc,rpcctl,set_zonefile_hash,wallet}
	                  ...

	Blockstack cli version 0.0.13.7

	positional arguments:
	  {balance,configure,deposit,import,info,lookup,migrate,names,ping,price,register,renew,revoke,set_advanced_mode,transfer,update,whois,consensus,delete_account,delete_immutable,delete_mutable,get_account,get_all_names,get_immutable,get_mutable,get_name_blockchain_history,get_name_blockchain_record,get_name_zonefile,get_nameops_at,get_names_in_namespace,get_names_owned_by_address,get_namespace_blockchain_record,get_namespace_cost,import_wallet,list_accounts,list_immutable_data_history,list_update_history,list_zonefile_history,lookup_snv,name_import,namespace_preorder,namespace_ready,namespace_reveal,put_account,put_immutable,put_mutable,rpc,rpcctl,set_zonefile_hash,wallet}
	    balance             Get the account balance
	    configure           Interactively configure the client
	    deposit             Display the address with which to receive bitcoins
	    import              Display the address with which to receive names
	    info                Get details about pending name commands
	    lookup              Get the zone file and profile for a particular name
	    migrate             Migrate a profile to the latest profile format
	    names               Display the names owned by local addresses
	    ping                Check server status and get server details
	    price               Get the price of a name
	    register            Register a name
	    renew               Renew a name
	    revoke              Revoke a name
	    set_advanced_mode   Enable advanced commands
	    transfer            Transfer a name to a new address
	    update              Set the zone file for a name
	    whois               Look up the blockchain info for a name
	    consensus           Get current consensus information
	    delete_account      Delete a particular account.
	    delete_immutable    Delete an immutable datum from a zonefile.
	    delete_mutable      Delete a mutable datum from a profile.
	    get_account         Get a particular account from a name.
	    get_all_names       Get all names in existence, optionally paginating
	                        through them
	    get_immutable       Get immutable data from a zonefile
	    get_mutable         Get mutable data from a profile
	    get_name_blockchain_history
	                        Get a sequence of historic blockchain records for a
	                        name
	    get_name_blockchain_record
	                        Get the raw blockchain record for a name
	    get_name_zonefile   Get a name's zonefile, as a JSON dict
	    get_nameops_at      Get the list of name operations that occurred at a
	                        given block number
	    get_names_in_namespace
	                        Get the names in a given namespace, optionally
	                        patinating through them
	    get_names_owned_by_address
	                        Get the list of names owned by an address
	    get_namespace_blockchain_record
	                        Get the raw namespace blockchain record for a name
	    get_namespace_cost  Get the cost of a namespace
	    import_wallet       Set the payment, owner, and (optionally) data private
	                        keys for the wallet.
	    list_accounts       List the set of accounts associated with a name.
	    list_immutable_data_history
	                        List all prior hashes of a given immutable datum
	    list_update_history
	                        List the history of update hashes for a name
	    list_zonefile_history
	                        List the history of zonefiles for a name (if they can
	                        be obtained)
	    lookup_snv          Use SNV to look up a name at a particular block height
	    name_import         Import a name to a revealed but not-yet-readied
	                        namespace
	    namespace_preorder  Preorder a namespace
	    namespace_ready     Mark a namespace as ready
	    namespace_reveal    Reveal a namespace and set its pricing parameters
	    put_account         Set a particular account's details. If the account
	                        already exists, it will be overwritten.
	    put_immutable       Put immutable data into a zonefile
	    put_mutable         Put mutable data into a profile
	    rpc                 Issue an RPC request to a locally-running API endpoint
	    rpcctl              Control the background blockstack API endpoint
	    set_zonefile_hash   Directly set the hash associated with the name in the
	                        blockchain.
	    wallet              Query wallet information

	optional arguments:
	  -h, --help            show this help message and exit
	{}

So, we will attempt to add some immutable data (this requires a write of an integrity hash to the blockchain) to the name record:

	Marks-MacBook-Pro-2:.blockstack mark.hennessy$ blockstack put_immutable
	usage: blockstack put_immutable [-h] name data_id data
	blockstack put_immutable: error: too few arguments

	Interactive prompt engaged.  Press Ctrl+C to quit
	Help for 'put_immutable': Put immutable data into a zonefile

	The name to receive the data ('name'): mahennessy.id
	The name of the data ('data_id'): mydata
	The JSON-formatted data to store ('data'): { "field1" : "value1", "field2" : "value2" }
	[2016-07-25 17:43:19,034] [ERROR] [blockstack:75] (31489) get_config() got an unexpected keyword argument 'config_path'
	Traceback (most recent call last):
	  File "/usr/local/bin/blockstack", line 62, in <module>
	    result = run_cli()
	  File "/usr/local/lib/python2.7/site-packages/blockstack_client/cli.py", line 317, in run_cli
	    result = method( args, config_path=config_path )
	  File "/usr/local/lib/python2.7/site-packages/blockstack_client/actions.py", line 1617, in cli_advanced_put_immutable
	    conf = config.get_config( config_path=config_path )
	TypeError: get_config() got an unexpected keyword argument 'config_path'
	{
	    "error": "Unexpected error:\nTraceback (most recent call last):\n  File \"/usr/local/bin/blockstack\", line 62, in <module>\n    result = run_cli()\n  File \"/usr/local/lib/python2.7/site-packages/blockstack_client/cli.py\", line 317, in run_cli\n    result = method( args, config_path=config_path )\n  File \"/usr/local/lib/python2.7/site-packages/blockstack_client/actions.py\", line 1617, in cli_advanced_put_immutable\n    conf = config.get_config( config_path=config_path )\nTypeError: get_config() got an unexpected keyword argument 'config_path'\n",
	    "help": "Try getting latest version of CLI with \"sudo pip install blockstack --upgrade\""
	}

Attempting to upgrade the CLI as suggested. This caused an update to cryptography package but all other dependent packages were already up-to-date. I tried again the above command to attach immutable data to the name record and got the same response. Also, attempted to try adding mutable data but this also failed with a different error complaining that the data supplied is not a dictionary.

### Conclusion

This was not intended to be a comprehensive transcript of the usage of Blockstack but more a dabble to get a feel for the technology. My sentiment at this moment is that it is pretty well thought out but not stable once you start to use its more advanced features relating to adding mutable and immutable data to extend the profile. Waiting around for your name to be registered is "blockchain transaction confirmation time" bound but in practice I do not see this as a major issue as it should not be very often that the user will register a new identity. Applications that infrequently update the immutable aspect of the identity profile should also not run into major issues but applications that do, could run into problems if they require fast read-back of the immutable data from their identity profile.

[Questions? Comments?](https://github.com/Charterhouse/charterhouse.github.io/issues/4)
