# Basic-Blazor-WASM-app-with-Paddle-checkout

## Is it possible to integrate the Paddle checkout with Blazor?

The short answer is yes, but you will need to use `jsRuntime` in order to run the Javascript functions.

There are two flavours of Blazor - Web Assembly or WASM (client-side) and Server. The following relates to Web Assembly, but the concepts are similar for the Server-side version.

---

### Include Paddle.js

Add paddle.js to the head of the wwwroot/index.html file as you would a regular HTML page [as per the help documents](https://developer.paddle.com/getting-started/build-a-paddle-checkout-in-10-minutes#:\~:text=%3Cscript%20src%3D%22https%3A//cdn.paddle.com/paddle/paddle.js%22%3E%3C/script%3E%0A%3Cscript%20type%3D%22text/javascript%22%3E%0A%09Paddle.Setup(%7B%20vendor%3A%201234567%20%7D)%3B%0A%3C/script%3E)​ (you can also add eventCallbacks as described [here](https://developer.paddle.com/reference/paddle-js/checkout-events)​ if you want)

---

### Create a class to pass data to checkouts

In Program.cs create a public class called Checkout inside the blazor_web namespace. This is going to allow us to pass data to Paddle when we call Paddle.Checkout.open:

```
public class Checkout
  {
    public string Product { get; set; }
    public string Email { get; set; }
    public string Country { get; set; }
    public string Postcode { get; set; }
    public string Override { get; set; }
  }
```

---

### Inject IJSRuntime to the page/view

In the .razor file where you want to include your checkout function, add `@inject IJSRuntime jsRuntime` to the top of the page in order to include the jsRuntime.

---

### Create a buy button

Create a button with an onclick event - e.g. `<button @onclick=ButtonClicked>Buy Now!</button>`

Define a private async Task called ButtonClicked in a @code{} block in the same .razor file:

`private async Task ButtonClicked()\
{\
    var checkout = new Checkout\
    {\
        Product = CheckoutProduct,\
        Email = CheckoutEmail,\
        Country = CheckoutCountry,\
        Postcode = CheckoutPostcode\
    };\
    await jsRuntime.InvokeVoidAsync("Paddle.Checkout.open", checkout);\
}`

---

### Pass the checkout values to the checkout

The CheckoutProduct, CheckoutEmail, CheckoutCountry and CheckoutPostcode values can either be defined in the code block statically, e.g. `public string CheckoutProduct = "12345";` or they can be defined through a form using @bind, e.g. `<input type="text" @bind="CheckoutEmail" @bind:event="oninput">`\
​
