**SaveStreams is the solution to saving streams in the web browser.
It is perfect for web apps where there's a need to save large amounts of data on devices with e.g. limited RAM.**

SaveStreams takes a different approach. Instead of saving data in client-side
storage or in memory you could now actually create a writable stream directly to
the file system (I'm not talking about chromes sandboxed file system or any other
web storage). This is accomplish by emulating how a server would instruct the
browser to save a file using some response header + service worker

**If the file you are trying to save comes from the cloud/server** use the server instead
of emulating what the browser does to save files on the disk using SaveStreams.
Add those extra Response headers and **don't use AJAX** to get it.

**If the file you are trying to save comes from the cloud/server** use the server instead
of emulating what the browser does to save files on the disk using SaveStreams.
Add those extra Response headers and **don't use AJAX** to get it.
If you can't change the headers then you may use StreamSaver
as a last resort. saveStreams and others alike are mostly for client
generated content inside the browser.


## Best practice

**Use https** if you can. That way you don't have to open the man in the middle
in a popup to install the service worker from another secure context. Popups are often blocked
but if you can't it's best that you **initiate the `createWriteStream`
on user interaction**. Even if you don't have any data ready - this is so that you can get around the popup blockers. (In secure context this don't matter)
Another benefit of using https is that the mitm-iframe can ping the service worker to prevent it from going idle. (worker goes idle after 30 sec in firefox, 5 minutes in blink) but also this won't mater if the browser supports [transferable streams](https://github.com/whatwg/streams/blob/master/transferable-streams-explainer.md) throught postMessage since service worker don't have to handle any logic. (the stream that you transfer to the service worker will be the stream we respond with)

**Handle unload event** when user leaves the page. The download gets broken when you leave the page.
Because it looks like a regular native download process some might think that it's okey to leave the page beforehand since it's is downloading in the background directly from some a server, but it isn't.