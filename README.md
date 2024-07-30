# YouTube Comment Deleter

YouTube doesn't provide an easy way to bulk-delete your comments without deleting your entire account. This script solves that.

## Dates
- Confirmed working as of July 29, 2024. Deleted 7 years worth of comments.

## How to use
1. Go to https://www.youtube.com/feed/history
2. On the right-hand side under "Manage all history", click Comments
3. Open a browser console
4. Paste the following code, press [Enter], and let 'er rip!

NOTE: You may need to refresh/repeat the steps a few times.


```
function simulateClick(element) {
    const evt = new MouseEvent('click', {
        bubbles: true,
        cancelable: true,
        view: window
    });
    element.dispatchEvent(evt);
}

function delay(time) {
    return new Promise(resolve => setTimeout(resolve, time));
}

async function deleteComments() {
    const deleteButtons = Array.from(document.querySelectorAll('svg')).filter(svg => {
        const path = svg.querySelector('path');
        return path && path.getAttribute('d') === "M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12 19 6.41z";
    }).map(svg => svg.closest('button'));

    console.log(`Found ${deleteButtons.length} delete buttons.`);

    for (let button of deleteButtons) {
        console.log('Attempting to click delete button...');
        simulateClick(button);
        await delay(4000);  // Wait for 4 seconds after each delete click
        console.log('Waited 3 seconds, moving to next.');
    }

    console.log('Finished processing all deletions.');
}

deleteComments();

```

## How it works
The issue with the other bulk-deleters is they just spam-simulate a "delete click." But if you refresh the page, you can see the comments weren't actually deleted. This script waits for a bit to allow the deletion to complete before continuing to the next comment. 
