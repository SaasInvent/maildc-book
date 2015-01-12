## Mongoose and Q Promises ##

Mongoose uses its own solution to Promises. Unfortunately it is not compatible with Q Promises.

But there is a workaround :

    You need to wrap your mongoose command into a Q Promise:

    Q(Conference.findOne({ id: 12 }).exec())
		.then (conference) ->
		    console.log conference
		.fail (err) ->
			console.log err
   


Credit goes to [this article.](http://blog.xebia.fr/2014/11/28/mongoose-les-promises-et-q/)

Here's some example code:

    express_app.post '/api/payplugipn', (req, res)=>
	    Q(Account.findOne({email : req.param('email')}).exec())
	    .then (account) ->
	        console.log "Account : " +  account
	        account.checkValidUntil()
	        account.addPremiumMois()
	        account.save (err, doc) =>
	            if err
	                console.log err
	            else
	                console.log "saved Account"
	    .then(WKB.updateInvoice(req))
	    .then(WKB.printInvoice(req))
	    .fail (err) ->
	       console.log err


This is the example code from WKBudateInvoice(req) :

  

     updateInvoice: (req) ->     
         order = req.param('order')            
         Q(Payplug.findOne({order : order} ).exec()) 
           .then (payplug) ->
              payplug.first_name = req.param('first_name')
              payplug.last_name = req.param('last_name')
              payplug.state = req.param('state')
              payplug.id_transaction = parseInt(req.param('id_transaction'), 10)
              payplug.save (err, doc) ->
                if err?
                   console.log err
                else
                 console.log "Payplug after save : " + doc
             .fail (err) ->
                 console.log err      