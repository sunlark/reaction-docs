# Orders
## Order Methods
All order related Meteor methods can be found in [/reaction-core/server/methods/orders.js](https://github.com/reactioncommerce/reaction-core/server/methods/orders.js)

### orders/inventoryAdjust
Called when the customer places an order. It's triggered by entering a credit card that that is not declined and clicking the check out button. The function loops through each Product in an order and adjusts the quantity for that product variant.

```javascript
  ReactionCore.Collections.Products.update({
    "_id": product.productId,
    "variants._id": product.variants._id
  }, {
    $inc: {
      "variants.$.inventoryQuantity": -product.quantity
    });
```

### orders/addOrderEmail
Called when a customer has checked out as a guest, then adds an email within the order confirmation page. This also adds an email field to `ReactionCore.Collections.Orders.email`

### orders/updateHistory
Called when any Order event occurs. The first occurance is when a user clicks on the newly created order, but also called  when the **begin** button is clicked or tracking number added etc. It extends the history object with additional fields to `ReactionCore.Collections.Orders.history`

```javascript
    "history": {
      event: event,
      value: value,
      userId: Meteor.userId(),
      updatedAt: new Date()
    }
```

### orders/shipmentTracking
Called when a tracking number has been entered and the **Add** button was clicked. This also triggers `addTracking` and `updateHistory`. This method verifies the order and tracking, then calls addTracking and updateHistory and updates the workflow/pushOrderWorkflow status.

### orders/addTracking
Called when a tracking number has been entered and the **Add** button has been clicked.  This updates `ReactionCore.Collections.Orders.shipping.shipmentMethod.tracking`

### orders/documentPrepare
Called when the **Download PDF** button is clicked or when the Adjustment _Approved_ button is clicked. This also calls updateHistory and updated that shipment is being prepared.

### orders/processPayment
This method calls the `processPayments` and also updates the workflow status.

### orders/processPayments
Determines the payment method and hits the payment API to capture the payment. If successful it updates `ReactionCore.Collections.Orders.payment.paymentMethod.transactionId` else it throws an error :

```javascript
if (result.capture) {
  transactionId = paymentMethod.transactionId;
  ReactionCore.Collections.Orders.update({
    "_id": orderId,
    "payment.paymentMethod.transactionId": transactionId
  }, {
    $set: {
      "payment.paymentMethod.$.transactionId": result.capture.id,
      "payment.paymentMethod.$.mode": "capture",
      "payment.paymentMethod.$.status": "completed"
    }
  });

} else {
  ReactionCore.Events.warn("Failed to capture transaction.", order, paymentMethod.transactionId);
  throw new Meteor.Error("Failed to capture transaction");
}
```

### orders/shipmentShipped
Called when payment is completed and updates the work flow to the coreShipmentShipped status.

### orders/orderCompleted
Called when the order has been completed. This updates the workflow status and also updated the order with the OrderCompleted Status.

### orders/shipmentPacking
Updates the workflow status that the shipment is being packed.

### orders/updateDocument
Updates the order with a reference to the specific doc created for shipping and label.

```javascript
ReactionCore.Collections.Orders.update(orderId, {
      $addToSet: {
        documents: {
          docId: docId,
          docType: docType
        }
      }
    });
```

### orders/addItemToShipment
This adds an item to the Orders.shipping.items array.

```javascript
ReactionCore.Collections.Orders.update({
      "_id": orderId,
      "shipping._id": shipmentId
    }, {
      $push: {
        "shipping.$.items": item
      }
    });
```

### orders/updateShipmentItem
This updates the items that are being associatated with a specific shipping id.

### orders/removeShipment
This method removed the shipment information from an order. It sets shipment object to null.

```javascript
ReactionCore.Collections.Orders.update(orderId, {
      $pull: {
        shipments: null
      }
    });
```

### orders/capturePayments
Cycles through each Orders paymentMethod and attempts to capture the payment. If successful it updates the Paymentmethod status to completed. Else it throws a server warning that payment was not captured.

### orders/updateShipmentTracking
updated the shipment tracking information.
