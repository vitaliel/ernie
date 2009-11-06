Ernie
=====

By Tom Preston-Werner (tom@mojombo.com)

Ernie is a BERT-RPC server implementation that uses an Erlang server to accept incoming connections, and then delegates the request to Ruby handlers.

See the full BERT-RPC specification at [bert-rpc.org](http://bert-rpc.org).

Ernie currently supports the following BERT-RPC features:

* `call` requests
* `cast` requests

Ernie was developed for GitHub and is currently in production use serving millions of RPC requests every day. The stability and performance have been exemplary.


Installation
------------

You must have Erlang installed before installing Ernie.

    $ gem install ernie -s http://gemcutter.org


Running
-------

    Usage: ernie [command] [options]
        -h, --handler HANDLER            Handler file
        -p, --port PORT                  Port
        -n, --number NUMBER              Number of handler instances
        -d, --detached                   Run as a daemon
        -P, --pidfile PIDFILE            Location to write pid file.
        -u, --user USERNAME              The user to run as
        -g, --group GROUP                The group to run as
    
    Commands:
      <none>                Start an Ernie server.
      reload-handlers       Gracefully reload all of the the ruby handlers
                            and use the new code for all subsequent requests.
      stats                 Print a list of connection and handler statistics.
    
    Examples:
      ernie -d -p 9999 -n 10 -h calc.rb
        Start the ernie server in the background on port 9999 with ten
        handlers, using the calc.rb handler file.
    
      ernie reload-handlers -p 9999
        Reload the handlers for the ernie server currently running on
        port 9999.

      ernie -d -p 9998 -n 5 -h calc.rb -u www-data -u www-data
        Similar to the above command to start the ernie server, but this
        one will run the Erlang server and the child handlers as the
        www-data user and group.

Example Handler
---------------

    require 'ernie'
    
    mod(:calc) do
      fun(:add) do |a, b|
        a + b
      end
    end


Example BERT-RPC call for above example
---------------------------------------

    -> {call, calc, add, [1, 2]}
    
    <- {reply, 3}


Using the BERTRPC gem to make calls to Ernie
--------------------------------------------

You can make BERT-RPC calls from Ruby with the [BERTRPC gem](http://github.com/mojombo/bertrpc):

    require 'bertrpc'
    
    svc = BERTRPC::Service.new('localhost', 8000)
    svc.call.calc.add(1, 2)
    # => 3


Contribute
----------

If you'd like to hack on Ernie, start by forking my repo on GitHub:

http://github.com/mojombo/ernie

To get all of the dependencies, install the gem first. The best way to get
your changes merged back into core is as follows:

1. Clone down your fork
1. Create a topic branch to contain your change
1. Hack away
1. Add tests and make sure everything still passes by running `rake`
1. If you are adding new functionality, document it in the README.md
1. Do not change the version number, I will do that on my end
1. If necessary, rebase your commits into logical chunks, without errors
1. Push the branch up to GitHub
1. Send me (mojombo) a pull request for your branch


Copyright
---------

Copyright (c) 2009 Tom Preston-Werner. See LICENSE for details.