# web-components-puppy-cards
## Assignment
Create a simple app with reusable components that displays three cards with basic functionality. Each card:

- displays a picture of a dog
- has a button that will alert a hello from the dog
- will have a toggle to show more details

## What is provided?
- A basic HTML file
- index.css and style.css (with styles),
- Folder pet-card with an index.js
- Folder of images
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Puppies!</title>
    <link rel="stylesheet" href="index.css" />
</head>

<body>
    Hello puppies!
    <script src="index.js"></script>
</body>

</html>
```

## What do we need to do?
- Run npx serve  to get our local server running
- In the pet-card create a class (PetCard) that extends HTMLElement. Export the class. Inside the constructor function: 
- Call the super function
- Create a this.attachShadow function and pass it an object of mode: open More on shadow DOM
- Create a template and append that to our shadowRoot template.content.cloneNode(true)); 

```html
class PetCard extends HTMLElement {
    constructor() {
        super();
        this.attachShadow({ mode: "open" });
        this.shadowRoot.appendChild(template.content.cloneNode(true));
    }
}

Outside of the class create the template variable using document.createElement 
Set the innerHTML  to:

<link rel="stylesheet" href="pet-card/style.css" />    
<div class="pet-card">
    <div class="avatar">
        <img />
    </div>
    <div class="details">
        <h2></h2>
        <div class="info">
            <p>Breed: <slot name="breed" /></p>
            <p>Age: <slot name="age" /></p>
        </div>
        <div class="actions">
            <button id="greet">Say Hi!</button>
            <button id="toggle">View Details</button>
        </div>
    </div>
</div>
```
This is all basic HTML aside from the slot that will be used to populate the details and age. This allows us to take the data and pass it as a child to our custom component. More on using slots

- In the top level index.js import the class
- Call window.customElements.define and pass the name by which we are going to call our element (pet-card) as the first parameter and pass our class (PetCard) as the second parameter. More on customElements
- In index.html replace "Hello puppies!" with our custom element

```html
import PetCard from "./pet-card/index.js";

window.customElements.define("pet-card", PetCard);
```

- Refresh the page and we should see the basic structure of our card. 
- Populate the data using the get attribute. For this we'll use the lifecycle method attributeChangedCallback(name, oldValue, newValue) This will give us the name of the element which is passed to our custom element, the old value and the new value. Add this callback under the constructor in our class. 
- Set the name
- Inside this callback function call the shadowRoot and target the .details h2  element and set it's innerText to the this.getAttribute function. Pass it the name of the pet ("name")
- Set the image
- Use the same process to update the src of the img element
- Use the same process to set the alt. Set the alt to the name. 
- To ensure we always get the updated data from this getAttribute function we need to call another static getter called observedAttributes(). 
- Under the constructor and above the attributeChangedCallback function add the observedAttributes function. static get observedAttributes() {} 
- Inside we are going to return the attributes we want to observe. Those will be our name and avatar attributes. 
```html
    static get observedAttributes() {
        return ["name", "avatar"]
    }

    attributeChangedCallback(name, oldValue, newValue) {
        this.shadowRoot.querySelector(".details h2").innerText =
            this.getAttribute("name");
        this.shadowRoot.querySelector(".avatar img").src =
            this.getAttribute("avatar");
        this.shadowRoot.querySelector(".avatar img").alt =
            this.getAttribute("name");
    }
```
- In the index.html file we can now pass the attributes into our custom element. 
- Refresh to make sure the values are coming through
- Go to the style.css file and uncomment the display:none on the details. 
- Now we will add an event listener to the details button to show the content when the button is clicked.
- We first need to make sure the custom component is completely mounted into the DOM. We can do this with the lifecycle method, connectedCallback() 
- Within the lifecycle method, add a click event to the button with the id #toggle 
```html
    connectedCallback() {
        this.shadowRoot.querySelector("#toggle").addEventListener("click", this.toggleInfo);
    }
```
- Set up the event function

- In the constructor set up a property showInfo  and set it to be false by default
- Inside the click event set the showInfo property to be the opposite of the current value
- Set the style on .info to display:block  if showInfo is true otherwise set to display: none 

- Update the button text to say "Hide Details" if showInfo is true and otherwise set the button text to "View Details"
```html
    toggleInfo = () => {
        this.showInfo = !this.showInfo;
        this.shadowRoot.querySelector(".info").style.display = this.showInfo ? "block" : "none";
        this.shadowRoot.querySelector("#toggle").innerHTML = this.showInfo ? "Hide Details" : "View Details";
    }
```
- Lastly, set up a click event for the button #greet inside of connectedCallback() 
- Set a string with a greeting and include the puppy's name. 
- Refresh and you should have a finished app!

## Notes
#### The Class has four basic lifecycle methods:

- constructor()  - Called when an instance of the element is created or upgraded
- connectedCallback()  - Called every time the element is inserted into the DOM
- disconnectedCallback()  - Called every time an element is removed from the DOM
- attributeChangedCallback(attributeName, oldValue, newValue)  - Called when an attribute is added, removed, updated or replaced

Shadow DOM allows us to create a DOM sub tree which is called inside our custom element. This helps us create styles and functions specific only to our element.



