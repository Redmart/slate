# Errors

The Partner API uses the following error codes:

Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request is semantically invalid, e.g. it contains a number where a string is expected or it's missing a mandatory parameter.
401 | Unauthorized -- Your oauth2 access-token is absent, invalid or expired.
403 | Forbidden -- Your oauth2 access-token is valid but its scope does not grant you access to this operation.
404 | Not Found -- The specified resource could not be found.
409 | Conflict -- Your request conflicts with the current state of the resource, e.g. you're setting a product stock's `quantityAtPickupLocation` to a value lower than its current `quantityScheduledForPickup` resulting in its `quantityAvailableForSale` being negative.
412 | Unprocessable Entity -- Your request is semantically valid but it cannot be processed, e.g. because its date parameter 'from' is greater than its 'till'.
422 | Precondition Failed -- One or more conditions given in the request header fields evaluated to false when tested on the server, e.g. the `if-match` header of your update-stock-lot request did not match the current stock on RedMart side.
429 | Too Many Requests -- You're triggering our rate limiter! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
