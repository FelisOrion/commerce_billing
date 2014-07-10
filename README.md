Commerce.Payments
=================

Payment processing library for Elixir. Based on [Shopify's](http://shopify.com) [ActiveMerchant](http://github.com/Shopify/active_merchant) ruby gem

Processing a Card
=================

```elixir
alias Commerce.Payments

credentials = {"sk_test_BQokikJOvBiI2HlWgH4olfQ2", ""}

{:ok, worker} = Payments.Worker.start_link(Payments.Gateways.Stripe, credentials: credentials)

card = %Payments.CreditCard{name: "John Smith",
                            number: "4111111111111111",
                            expiration: {2017, 12},
                            cvc: "123"}

address = %Payments.Address{street1: "123 Main",
                            city: "New York",
                            region: "NY",
                            country: "US",
                            postal_code: "11111"}

worker.authorize(199.95, card, billing_address: address, description: "Amazing T-Shirt")
```

Road Map
================

- Support multiple gateways (PayPal, Stripe, Authorize.net, Braintree etc..)
- Support gateways that bill directly and those that use html integrations.
- Each gateway is hosted in a worker process and supervised.
- Workers can be pooled. (using poolboy)
- Workers can be spread on multiple nodes
- The gateway is selected by first calling the "Gateway Factory" process. The "Gateway Factory" decides which gateway to use. Usually it will just be one type based on configuration setting in mix.exs (i.e. Stripe), but the Factory can be replaced with something fancier. It will enable scenarios like:
    - Use one gateway for visa another for mastercard
    - Use primary gateway (i.e PayPal), but when PayPal is erroring switch to secondary/backup gateway (i.e. Authorize.net)
    - Currency specific gateway, i.e. use one gateway type for USD another for CAD
- Retry on network failure
