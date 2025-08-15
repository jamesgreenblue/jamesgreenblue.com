---
title: Identify your users by name in Amplitude
slug: name-users-in-amplitude
date: 2025-08-12
tags: ["amplitude", "analytics", "GDPR", "PII", "tampermonkey", "script", "AI", "Gemini"]
---

Amplitude is a decent product analytics tool, but there is no compromise when it comes to identification. User IDs are fixed forever and all data is immutable once it lands in the platform – names and email addresses are not an option.

Knowing the name of a user can unlock insight by giving context to usage patterns, especially in early-stage projects with a handful of users who are working at the same company.

I'm not aware of a way to make annotations directly in Amplitude; all data has to arrive over their API. Anyway, doing this would expose personally identifiable information (PII) to a third party.

To solve this, [I worked with Gemini](/blog/2025/credit-the-vibes/) to develop a [Tampermonkey](https://tampermonkey.net) userscript that finds and replaces the user ID with text of your choosing. For the uninitiated, Tampermonkey is a browser extension that lets you change how websites function.

To do the same simply: 

1. Install [Tampermonkey](https://chromewebstore.google.com/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo)
2. Copy the code below:

```js {linenos=true}
// ==UserScript==
// @name         Amplitude named user profiles
// @namespace    https://jamesgreenblue.com/
// @version      0.1
// @description  Finds and replaces user profile names in Amplitude
// @author       James Greenhalgh, with significant contributions from an AI Assistant (Gemini)
// @match        *://app.amplitude.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    var replaceArry = [
        [/user_cva3bbm9i077dn133b2h/gi, 'james.g'],
        [/user_cva4gge9i077spp33b3h/gi, 'joe.b'],
        [/user_cva64re9i078i0h33b41/gi, 'jane.d'],
        // Add more replacements here
    ];
    var numTerms = replaceArry.length;

    function replaceTextInNode(node) {
        if (node.nodeType === Node.TEXT_NODE) {
            let oldTxt = node.nodeValue;
            let newTxt = oldTxt;
            for (let i = 0; i < numTerms; i++) {
                newTxt = newTxt.replace(replaceArry[i][0], replaceArry[i][1]);
            }
            if (newTxt !== oldTxt) {
                node.nodeValue = newTxt;
            }
        } else if (node.nodeType === Node.ELEMENT_NODE && node.childNodes) {
            node.childNodes.forEach(replaceTextInNode);
        }
    }

    // Initial scan of the document
    replaceTextInNode(document.body);

    // Observe for future changes
    const observer = new MutationObserver(mutations => {
        mutations.forEach(mutation => {
            mutation.addedNodes.forEach(node => {
                if (node.nodeType === Node.ELEMENT_NODE || node.nodeType === Node.TEXT_NODE) {
                    replaceTextInNode(node);
                }
            });
            // For text content changes within existing nodes
            if (mutation.type === 'characterData' && mutation.target.parentNode) {
                replaceTextInNode(mutation.target.parentNode);
            }
        });
    });

    observer.observe(document.body, { subtree: true, childList: true, characterData: true });
})();
```

3. Edit the rows in `replaceArry` to match the ID you see in Amplitude with the name you would like to see instead. Add more replacements if necessary. I suggest avoiding full names to minimise PII on your local machine.
4. Save and enable the script in Tampermonkey
5. Gain insight

This userscript is also [published on GitHub](https://github.com/jamesgreenblue/tm-name-users-in-amplitude) if you would like to fork your own version or report issues.