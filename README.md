# YouTube Comment Deleter

YouTube doesn't provide an easy way to bulk-delete your comments without deleting your entire account. This script solves that.

## How to use
1. Go to https://www.youtube.com/feed/history
2. On the right-hand side under "Manage all history", click Comments
3. Open a browser console
4. Paste the following code, press [Enter], and let 'er rip!


```
// Function to simulate a click event
function simulateClick(element) {
    const event = new MouseEvent('click', {
        view: window,
        bubbles: true,
        cancelable: true
    });
    element.dispatchEvent(event);
}

// Function to wait for the deletion confirmation to appear and then disappear
function waitForDeletionConfirmation(next) {
    let lastSeen = Date.now();
    const checkExistence = setInterval(() => {
        const confirmation = Array.from(document.querySelectorAll('div')).find(div => div.textContent.includes('1 item deleted'));
        if (confirmation) {
            console.log('Confirmation found.');
            lastSeen = Date.now(); // Update last seen time
        } else if (Date.now() - lastSeen > 1000) { // Wait for 1 second after last seen
            console.log('Confirmation gone, proceeding.');
            clearInterval(checkExistence);
            next();
        }
    }, 100); // Check every 100 milliseconds
}

// Delete all comments
function deleteComments() {
    // Select all the delete buttons based on the SVG path within them
    const deleteButtons = Array.from(document.querySelectorAll('svg')).filter(svg => {
        const path = svg.querySelector('path');
        return path && path.getAttribute('d') === "M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12 19 6.41z";
    }).map(svg => svg.closest('button')); // Get the closest button ancestor

    let index = 0; // To keep track of the current button being processed

    function processNextButton() {
        if (index < deleteButtons.length) {
            simulateClick(deleteButtons[index++]);
            waitForDeletionConfirmation(processNextButton);
        } else {
            console.log('Finished deleting comments or no more delete buttons found.');
            // Check for the next page
            const nextPageButton = document.querySelector('[aria-label="Next page"]');
            if (nextPageButton) {
                console.log('Moving to next page of comments...');
                simulateClick(nextPageButton);
                setTimeout(deleteComments, 3000); // Wait for page to load
            }
        }
    }

    processNextButton();
}

// Start deleting comments
deleteComments();
```

## How it works
The issue with the other bulk-deleters is they just spam-simulate a "delete click." But if you refresh the page, you can see the comments weren't actually deleted. This script waits for the YouTube callback that confirms the actual comment deletion before continuing to the next comment. 
