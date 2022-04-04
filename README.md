# Use Remote-Storage, a Community-Derived FOSS, Offline-First, Un-Hosted, Zero-Back-End for Your Web Product  ::  a How-To Tutorial Guide.



This tutorial will cover how to very easily make an offline-first Web application without being responsible for your user's data nor back-end maintenance.

We start by learning how to leverage [Remote Storage](https://remotestorage.io/) (RS) &mdash; which gives us everything but the ability to make money.  Later on we switch focus to an RS extension that, just as easily as the rest of RS, allows us to offer in-app purchases.  Why switch focus to having an app that makes money?  So you can keep the lights on and have time to make more apps, of course.



[Remote Storage](https://remotestorage.io/) (RS) is a community derived effort [that's many things](https://remotestoragejs.readthedocs.io/en/latest/why.html).  Most of which are easily demonstrable with this here hands-on tutorial.

With the help of the [remoteStorage.js](https://www.npmjs.com/package/remotestoragejs) NPM library RS becomes an offline-first back-end for your Web application.  Offline-first as your users' data is synced with the back-end when they're online but leverages browsers' local-storage when they're disconnected.  All without any effort from you, the app developer.



![](./assets/intro.png)



The RS ecosystem is a Bring-Your-Own-Data (BYOD) [un-hosted](https://unhosted.org/) concept:  your users authenticate and authorize with any of the RS hosts in the ecosystem, or their own, and connect to your application with their data, that they bring from the RS provider of their choosing.  You, the app developer, don't own and are not responsible for their data.  They, your users, have freedom of where their data lives and how safe it is.  For all intents and purposes the RS provider can be a public offering such as [overhide.io](https://rs.overhide.io) or [other RS servers](https://remotestorage.io/servers/), or the user's self-hosted system.

If you're into devops, the RS ecosystem is an ecosystem of self-hosted clusters that are stood up by the community for everyone to use.  You can stand up a private cluster just for your app or for the public.  You can provide RS storage for free or charge a fee in dollars or ethers.  To stand up a paid server look no further than the [Lucchetto RS server](https://github.com/overhide/armadietto/blob/master/lucchetto/README.md), batteries included.

All the code is Free Open-Source Software (FOSS).  The community is strongly pro-openness and empowering data self-ownership.

The first part of the tutorial is a hands-on materialization of all of the above in a simplistic Web app.  In the second part we will extend our example to enable in-app purchases.  We will leverage RS &mdash; albeit with the [Lucchetto bolt-on](https://www.npmjs.com/package/lucchetto) &mdash; to easily receive payments in dollars and cryptos for a an optional feature.



------

Questions, comments?  Let's discuss at [RS forums](https://community.remotestorage.io/) or [r/overhide](https://www.reddit.com/r/overhide/).

------



## Simple Code Listings

To follow along you need to be comfortable with basic HTML, JavaScript as it relates to the DOM, [git](https://git-scm.com/), and [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).  

For brevity, full code listings are not part of this write-up, but are referenced in each section from the code callouts:



> **</>**  FULL CODE 
>
> **[!!]**  RENDERED PREVIEW



All code listings are at <a target="_blank" href="https://github.com/overhide/remotestorage-tutorial">the tutorial's github repo</a>.

You don't have to do this next step &mdash; you can just see what we're talking about with the provided "RENDERED PREVIEW" links &mdash; but to render the HTML previews yourself, in some folder on your local computer:

``` 
git clone https://github.com/overhide/remotestorage-tutorial.git
cd remotestorage-tutorial
npm install -g http-server
http-server
```

Now the two HTML files we'll talk about are available to be opened with your browser on port 8080.  Open them:

​	open http://localhost:8080/1-app.html

​	open http://localhost:8080/2-app+iaps.html



## Our Application

Let's take a look at our example study app:



> **</>**  <a target="_blank" href="https://github.com/overhide/remotestorage-tutorial/blob/master/1-rs.html">FULL CODE</a> 
>
> **[!!]**   <a target="_blank" href="https://overhide.github.io/remotestorage-tutorial/1-rs.html">RENDERED PRVIEW</a>



After visiting the <a target="_blank" href="https://overhide.github.io/remotestorage-tutorial/1-rs.html">RENDERED PRVIEW</a> we see a *remote storage widget* at the upper left corner, some informative text, and three buttons at the bottom to select a cake.  The white space in the middle is where our current cake selection will appear.  It's blank at the moment as we have never selected a cake yet:



![image-20220403190513909](C:\jj\src\remotestorage-tutorial\assets\image-20220403190513909.png)



You can click on the buttons at the bottom and see a cake being selected.

With reference to the <a target="_blank" href="https://github.com/overhide/remotestorage-tutorial/blob/master/1-rs.html">FULL CODE</a> , each button has an `onClick` handler that calls the `setState` global function:



```
<button ... onClick="setState({...appstate, cake_choice: 'Black Forest'})">Set "Black Forest"</button>
...
<button ... onClick="setState({...appstate, cake_choice: 'Carrot'})">Set "Carrot"</button>
...
<button ... onClick="setState({...appstate, cake_choice: 'Tiramisu'})">Set "Tiramisu"</button>
```
<p align = "center">1-rs.html :: lines 32-38</p><br/>


The `setState` global function simply sets cake names to the `client`:



```
function setState(newState) {
  client.storeObject('appstate', '/appstate', newState);
}    
```

<p align = "center">1-rs.html :: lines 94-96</p><br/>


The parameters simply state, some object `newState` matching the JSON schema `'appstate'` should be stored as a JSON object at the path `'/appstate'`.  The pathing and schemas should make sense shortly.

Firstly, the `client` is our application's state store, initialized earlier in the code:



```
![one-way](C:\jj\src\remotestorage-tutorial\assets\one-way.png)const remoteStorage = new RemoteStorage({changeEvents: { local: true, window: true }});
remoteStorage.access.claim('remotestorage-tutorial', 'rw');     
...
const client = remoteStorage.scope('/remotestorage-tutorial/');
```

<p align = "center">1-rs.html :: lines 65-68</p><br/>

>  For the full documentation on `RemoteStorage` construction and `client` instantiation read the docs:
>
> - https://remotestoragejs.readthedocs.io/en/latest/js-api/remotestorage.html
> - https://remotestoragejs.readthedocs.io/en/latest/js-api/base-client.html



In brief, we instantiate `RemoteStorage` with full caching, remote synching, and state change notifications.  

The `remoteStorage.access.claim(..)` constrains that anytime a user connects their storage account to our application, the user must agree to read and write access (`'rw'`) for the `'remotestorage-tutorial'` namespace.  This doesn't concern us for now:  we haven't yet tried connecting to a remote server.

The actual `client` is scoped to the `/remotestorage-tutorial/` path for its interactions.  Notice that the path repeats the claim's namespace.

With these instantiations in place we're ready to use our applications unique namespace, 'remotestorage-tutorial', in both local and remote stores.



> Calling it a namespace and pretending that it needs to be unique is an over-simplification.  There is a whole gamut of interactions possible via sharing these paths as per RS data-modules.  But that's a more advanced topic.
>
> https://remotestoragejs.readthedocs.io/en/latest/data-modules.html



Let's move on to the importance of the `{ local: true, window: true}` options passed into the `RemoteStorage` constructor.  With these we're asking that, as the resultant `client` tracks our application state, it update our rendering widgets with a one-way state flow.

We've already seen the `client` receiving new state via the `client.storeObject(..)`.  Fully decoupled from these writes we have the state change listener:



```
client.on('change', (event) => {
  if (event.relativePath === '/appstate') {
    appstate = event.newValue;
	document.getElementById('choice').innerHTML = appstate.cake_choice;
  }
});   
```

<p align = "center">1-rs.html :: lines 82-87</p><br/>


Here we're registering an event handler against the `client`.  This code will get called on every local in-browser or remote change to any object under our previously registered scope (path).

In our handler above we're only interested in changes to the `/appstate` path.

On an update we simply set the value of our `#choice` `div` to the new cake choice.



What we're achieving is a one-way state flow through our `client`:



![](C:\jj\src\remotestorage-tutorial\assets\one-way.png)





What we're also transparently achieving is reliable storage of the app's state in user's storage:  both local and remote.  In fact, if you press F5 in your <a target="_blank" href="https://overhide.github.io/remotestorage-tutorial/1-rs.html">RENDERED PRVIEW</a>, the `client` will cause a `change` notification to our handler from the browser's local-storage.



Let's connect to a *remote-storage* server to ensure we sync our application changes outside of our current browser.

For this tutorial we will use the [@test.rs.overhide.io](https://test.rs.overhide.io) server, but [there are others](https://remotestorage.io/servers/).

[@test.rs.overhide.io](https://test.rs.overhide.io) is a free *remote-storage* server meant for testing and playing around.  It pretends to require money but it's all fake make-pretend US dollar transactions and testnet Ethereum transactions.

You can stand up the exact same server on your own machine from the [Armadietto+Lucchetto](https://github.com/overhide/armadietto) repository, or just use [@test.rs.overhide.io](https://test.rs.overhide.io) for now.

Click on the *remote-storage* widget to start logging in:



![image-20220403203124141](C:\jj\src\remotestorage-tutorial\assets\image-20220403203124141.png)



In the input box type in `@test.rs.overhide.io` and click on "Connect".

You're presented with a bunch of login options.  For now, scroll all the way to the bottom to the "secret token" card and click on "generate new".  The system will generate a new secret token for your login.  Save this in your browser's password manager when prompted, or copy it (the little blue clipboard) and save it off manually.



![image-20220403203457592](C:\jj\src\remotestorage-tutorial\assets\image-20220403203457592.png)



Click "continue".

You're prompted for a payment through Stripe.com.  This is a fake US dollars payment on the Stripe.com testnet.  



![image-20220403203627301](C:\jj\src\remotestorage-tutorial\assets\image-20220403203627301.png)



Enter fake information in the fields as per the instructions on screen:



![](C:\jj\src\remotestorage-tutorial\assets\visa.png)



Now you're back in our cake example app and you're connected with your newly acquired *remote-storage*.

You can use the exact same storage in [any other RS application](https://remotestorage.io/apps/).  Hope you saved off your secret token!  Your browser should remember, but other browsers won't.

To make your life easier, your best bet is to likely use social login providers on that login screen, or an Ethereum wallet if you can connect it.  Play around with the various options.



Now, as you click on the various cakes, you should see the *remote-storage* widget spin and sync: it's synching to your newly acquired *remote-storage*.



Before we conclude this section, let's get back to our `appstate` object as it's saved in the `client`:



```
client.storeObject('appstate', '/appstate', newState);
```

<p align = "center">1-rs.html :: line 95</p><br/>

We mentioned that the first parameter `'appstate'` indicates the JSON schema expected of the third parameter.  This JSON schema is registered against the `client`:

``` 
const appstate_schema = {
  type: 'object',
  properties: {
	cakce_choice: {
	  type: 'string'
	}
  },
  required: ['cake_choice']
};
...
client.declareType('appstate', appstate_schema);
```

<p align = "center">1-rs.html :: lines 49-57 and 77</p><br/>

The `'appstate'` schema with the value from `newState` is saved under the path `/appstate`, which is a path relative to the scope path of our `client`.  Hence we can think of the object being at `/remotestorage-tutorial/appstate`.



## Our Application With In-App Purchases

Now that we're comfortable leveraging *Remote Storage* to drive and cache our application's state, let's add in-app purchases to the mix:



> **</>**  <a target="_blank" href="https://github.com/overhide/remotestorage-tutorial/blob/master/2-iaps.html">FULL CODE</a> 
>
> **[!!]**   <a target="_blank" href="https://overhide.github.io/remotestorage-tutorial/2-iaps.html">RENDERED PRVIEW</a>



The main difference now is the in-app purchase button offered to your users at the bottom of our app, as per the new <a target="_blank" href="https://overhide.github.io/remotestorage-tutorial/2-iaps.html">RENDERED PRVIEW</a>:



![image-20220403205607892](C:\jj\src\remotestorage-tutorial\assets\image-20220403205607892.png)



The new "Buy Cake NFT" for $3 provides a nice value-add to your customer.

The action of this button will differ based on several factors of your user's *remote-storage* connection.  At the end of the day, however, the button will authorize, subsequently trigger an event, prompting our app to download a piece of data that we &mdash; the app developers &mdash; setup (well, I setup for this example).

If you're following along as the user that just logged into the [@test.rs.overhide.io](https://test.rs.overhide.io) *remote-storage*, then you should still be logged into this new page with our valid *remote-storage* connection:  it's the same domain hence the same token authorizes.  Since [@test.rs.overhide.io](https://test.rs.overhide.io) is a so called *Lucchetto* extended RS server &mdash; meaning it plays nice with this new in-app purchase button from the https://pay2my.app widgets &mdash; our click-through should be seamless.  We'll just need to top-up the $3 for the new IAP.

Why are we paying again?

Keep in mind that the earlier $1.99 payment was to the [@test.rs.overhide.io](https://test.rs.overhide.io) *remote-storage* provider:  for the storage.  This $3 payment is for the extra in-app value add.



> If your user logged into this app with a regular RS server &mdash; not *Lucchetto* extended &mdash; the IAP will work nearly just as well.  The notable difference being extra prompts whenever the user revisits the app; as the IAP button cannot leverage non-extended RS authorization for payments:  regular RS servers return tokens without the extra metadata.



Regardless, clicking on the button will eventually lead to the following handler executing (from the <a target="_blank" href="https://github.com/overhide/remotestorage-tutorial/blob/master/2-iaps.html">FULL CODE</a>):



```
window.addEventListener('pay2myapp-appsell-sku-clicked', async (e) => { 
  try {
	const result = await lucchetto.getSku(`https://test.rs.overhide.io`, e.detail);
	alert(result);
  } catch (e) {
	console.error(`error :: no file for sku ${e.detail.sku}`);
  }      
}, false);    
```

<p align = "center">2-iaps.html :: lines 140-147</p><br/>

The `'pay2myapp-appsell-sku-clicked` event comes from the https://pay2my.app widgets, which are the same widgets providing authentication and authorization in our [@test.rs.overhide.io](https://test.rs.overhide.io) server earlier.  



> The `pay2myapp-appsell-sku-clicked` event is documented as part of the [pay2myapp-appsell](https://github.com/overhide/pay2my.app#pay2myapp-appsell-) Web component.  



Reading the handler code we see that we use the event to retrieve a `result` via `lucchetto.getSku(..)` and `alert(result)` it out to the user.  The `lucchetto.getSku(..)` call provides the event's `detail` object and knows how to interpret it to retrieve the right content from the *Lucchetto* extended RS server provided as the first parameter, `'https://test.rs.overhide.io'` in our case.  We'll cover how to add this SKU content to the RS server later.

That `lucchetto` object used to `lucchetto.getSku(..)` is the piece of glue that connects *remote-storage* authorization to these IAP buttons in your application.  We really only use it in two places, this `lucchetto.getSku(..)` and earlier, to instantiate and initialize:



```
const lucchetto = new Lucchetto(remoteStorage, true, document.getElementById('demo-hub'));
```

<p align = "center">2-iaps.html :: line 105</p><br/>

When initializing `lucchetto` we provide the constructor with the *remote-storage* instance we normally use, `remoteStorage`.  The second parameter, the `true` boolean, indicates we're using testnets.  In a live production system you'd set this to `false`.  Finally, the last parameter is the `HTMLElement` reference of the [pay2myapp-hub](https://github.com/overhide/pay2my.app#pay2myapp-hub-) Web component.  You do not need to concern yourself with the nitty gritty, but let's look at the three Web components now.



```
<pay2myapp-hub id="demo-hub" apiKey="0x6cc3b096ef53d2e70152bed8f34448ddfaae34b3aa745b69a1d42f083a44080b" isTest noCache></pay2myapp-hub>      

<pay2myapp-login  
  hubId="demo-hub"
  overhideSocialMicrosoftEnabled
  overhideSocialGoogleEnabled
  overhideWeb3Enabled
  ethereumWeb3Enabled
  overhideSecretTokenEnabled>
</pay2myapp-login>

<pay2myapp-appsell 
  hubId="demo-hub" 
  sku="buy-nft"
  priceDollars="3"
  authorizedMessage="Cake NFT"
  unauthorizedTemplate="Buy Cake NFT ($${topup})"
  ethereumAddress="0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b"
  overhideAddress="0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b">
</pay2myapp-appsell>
```

<p align = "center">2-iaps.html :: lines 55-74</p><br/>

The first Web component is the before mentioned [pay2myapp-hub](https://github.com/overhide/pay2my.app#pay2myapp-hub-).  It's labelled with an ID `"demo-hub"` so it can be referenced from the other components ([or programatically](https://github.com/overhide/pay2my.app#setting-the-pay2myapp-hub-programatically) in bigger apps with frameworks).  The hub is the main component that communicates with ledgers and services all the other components.  The `isTest`  attribute specifies that the hub will communicate with testnet ledgers.  Leave this attribute out for production deployments.  The `noCache` attribute asks the system not to cache credentials:  considering we use *remote-storage* to do that for us.  Finally, the hub is provided with an `apiKey`.  An `apiKey` is your, the developer's, key to access the ledgers.  It's not a big deal to get an API key and there aren't really any restrictions besides the normal rate limits (see https://pay2my.app).  



> To get your own `apiKey` for your application visit https://token.overhide.io/register.
>
> Ensure to get the right key for your testnet app and a production key for your live app deployment.



The second Web component ties into our hub via `hubId` and specifies all the login methods available to our app, it's pretty self explanatory.

The third component is the actual upsell (app-sell?) button.  it specifies the `sku` &mdash; the tag given to the feature we're selling.  The `priceDollars` is the cost.  The cost is always in US dollars and is automatically converted to ethers if need be.  The `authorizedMessage` is the button-text when authorized.  The `unauthrizedTemplate` can include the `$${topup}` placeholder for the outstanding dollar amount.  

The `ethereumAddress` and `overhideAddress` are both Ethereum public addresses used to receive payments, but on two separate ledgers.  Ethereum is self explanatory.  The *overhide* ledger is a US dollar receipts ledger that uses Ethereum addresses but does not do value transfers.  Value transfers are through Stripe.com.  



> There are many more configurations of these buttons as described in the [pay2myapp-appsell](https://github.com/overhide/pay2my.app#pay2myapp-appsell-) documentation.



Let's focus on these addresses a bit more.  This is a feature upsell button that, when clicked, produces the previously mentioned `pay2myapp-appsell-sku-clicked` event and leads us to download a value using `lucchetto.getSku(..)`.   Authorization here means:  sufficient payments were made from our user's address to either the `ethereumAddress` for ethers or the `overhideAddress` for dollars.



Let me describe what I did to make this address of `0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b` availalble to this Web component, and hopefully this will make sense for when you want to add your own.



``` 
  ethereumAddress="0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b"
  overhideAddress="0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b">
```

<p align = "center">2-iaps.html :: lines 72-73</p><br/>

The process is the same for mainnet/prod/live as for testnet/fake, except with different networks.  

First and foremost I setup the [MetaMask](https://metamask.io/) wallet in my browser and one of my Ethereum addresses is `0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b`.  

By virtue of having this address, I'm already setup to receive ethers.  I just need to provide this address for `ethereumAddress`.

For US dollars it's a bit more involved as we need to connect this Ethereum address to a Stripe.com account.

We point our browser at [testnet onboarding](https://test.ledger.overhide.io/onboard) and start the onboarding.  I connected my [MetaMask](https://metamask.io/) wallet to the onboarding page so I can use its addresses.  

During the Stripe.com account setup simply skip the form:  unfortunately when you're ready for production you cannot just skip this Stripe.com part of onboarding.

![](assets/stripe_test.png)



Once onboarded, the [overhide US dollars ledger](https://overhide.io) will record US dollars payments through Stripe.com between your users' addresses and your onboarded address, enabling *Ledger-Based Authorizations*.



> For production onboarding with Ethereum simply use the same address, but with the *mainnet*.
>
> For the *overhide* US dollars ledger you'll need to do a full onboarding with [the production flow](https://ledger.overhide.io/onboard).



Now we're onboarded.  We know how these addresses factor in.  But what about the SKU data?

If you recall earlier, upon authorization, we retrieved data via `lucchetto.getSku(..)`.  Before that we created a [pay2myapp-appsell](https://github.com/overhide/pay2my.app#pay2myapp-appsell-) button specifying the `sku` and `priceDollars`.

How do we set this up?

Turns out the SKUs are simply *remote-storage* stored pieces of `plain/test` data served by *Lucchetto* extended RS servers, such as  [@test.rs.overhide.io](https://test.rs.overhide.io).  

To configure them we simply need to [use a RS app](https://overhide.github.io/armadietto/lucchetto/onboard.html#).



> As of this writing...
>
> Configure your testnet SKUs with  [@test.rs.overhide.io](https://test.rs.overhide.io)..
>
> Configure your production SKUs with  [@rs.overhide.io](https://rs.overhide.io).
>
> Check [the RS servers page](https://remotestorage.io/servers/) for more options, if any.



The [Lucchetto SKU onboard app](https://overhide.github.io/armadietto/lucchetto/onboard.html#) should be self explanatory.  You connect to a *Lucchetto* extended RS server such as one of the above listed.  The connected address must match the `ethereumAddress` and the `overhideAddress`.  If you made them different, you'll need to maintain separate RS connections to manage your SKUs.  For sanity, likely best to keep them the same.

In my case, I'd connect to  [@test.rs.overhide.io](https://test.rs.overhide.io) with `0xd6106c445A07a6A1caF02FC8050F1FDe30d7cE8b` from my crypto wallet.

Once connected the app lets you list existing SKUs, delete them, but most important, insert new ones using the "UPSERT" card.  Simply fill in the *price*, `3` in our case ($3), the *within*, `0` in our case (indefinite), and the *SKU*, `buy-nft` in our case to match the button.  Finally, set the *data*, which is the content returned from the `lucchetto.getSku(..)` call.  For our purposes, I set  it to `The cake is a lie!`.



![image-20220403232748130](C:\jj\src\remotestorage-tutorial\assets\image-20220403232748130.png)



Once you "UPSERT" you should see a confirmation, and you're done.  The SKU is ready to use:



![image-20220403232842915](C:\jj\src\remotestorage-tutorial\assets\image-20220403232842915.png)



*Remote-storage* is a fantastic way to write your applications.

Adding in-app purchases hopefully means you'll be able to get some kick-backs for the wonderful work you do!