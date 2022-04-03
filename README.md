# Use Remote-Storage, the Offline-First, Un-Hosted, Zero-Back-End, FOSS, for Your Web Product  :  a How-To Tutorial Guide.



[Remote Storage](https://remotestorage.io/) (RS) is a community derived effort [that's many things](https://remotestoragejs.readthedocs.io/en/latest/why.html).  Most of which are easily demonstrable with this here hands-on tutorial.

With the help of the [remoteStorage.js](https://www.npmjs.com/package/remotestoragejs) NPM library RS becomes an offline-first back-end for your Web application.  Offline-first as your users' data is synced with the back-end when they're online but leverages browsers' local-storage when they're disconnected.  All without any effort from you, the app developer.

The RS ecosystem is a Bring-Your-Own-Data (BYOD) [un-hosted](https://unhosted.org/) concept:  your users authenticate and authorize with any of the RS hosts in the ecosystem, or their own, and connect to your application with their data, that they bring from the RS provider of their choosing.  You, the app developer, don't own and are not responsible for their data.  They, your users, have freedom of where their data lives and how safe it is.  For all intents and purposes the RS provider can be a public offering such as [overhide.io](https://rs.overhide.io) or [other RS servers](https://remotestorage.io/servers/), or the user's self-hosted system.

If you're into devops, the RS ecosystem is an ecosystem of self-hosted clusters that are stood up by the community for everyone to use.  You can stand up a private cluster just for your app or for the public.  You can provide RS storage for free or charge a fee in dollars or ethers.  To stand up a paid server look no further than the [Lucchetto RS server](https://github.com/overhide/armadietto/blob/master/lucchetto/README.md), batteries included.

All the code is Free Open-Source Software (FOSS).  The community is strongly pro-openness and empowering data self-ownership.

Before we get coding &mdash; as this is a hands-on tutorial &mdash; one last benefit of using RS, albeit with the [Lucchetto bolt-on](https://www.npmjs.com/package/lucchetto), is the simplicity of adding in-app purchases to your Web app.  We'll talk about this at the end of this write-up.



------

Questions, comments?  Let's discuss at [RS forums](https://community.remotestorage.io/) or [r/overhide](https://www.reddit.com/r/overhide/).

------



## Simple Code Listings

For brevity, full code listings are not part of this write-up, but are referenced in each section from the code callouts:



> **</>**  FULL CODE 
>
> **[!!]**  RENDERED PREVIEW



All front-end code listings are at <a target="_blank" href="https://github.com/overhide/armadietto/tree/master/lucchetto/howto">this tutorial's github repo</a>.

To follow along you need to be comfortable with basic HTML and JavaScript as it relates to the DOM.



## Study App

Let's take a look at our example study app:



> **</>**  <a target="_blank" href="https://github.com/overhide/pay2my.app/blob/master/howto/intro/code/1_first.html">FULL CODE</a> 
>
> **[!!]**   <a target="_blank" href="https://overhide.github.io/pay2my.app/howto/intro/code/1_first.html">RENDERED PRVIEW</a>



 With reference to the above code, let's go through the notable bits.

