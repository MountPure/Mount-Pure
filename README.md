import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Select, SelectTrigger, SelectContent, SelectItem } from "@/components/ui/select";
import { Badge } from "@/components/ui/badge";

// List of available items for selection
const availableItems = [
  "300 ml", "500 ml", "1L", "1.5L", "5L", "5Gal", "500 SPK", "330 SPK", "750 SPK"
];

export default function OrderTracking() {
  // State to manage orders
  const [orders, setOrders] = useState([]);
  // State for customer name input
  const [customerName, setCustomerName] = useState("");
  // State for selected items (multiple items allowed)
  const [selectedItems, setSelectedItems] = useState([]);
  // State for quantity input
  const [quantity, setQuantity] = useState("");

  // Simulate live updates every 5 seconds
  useEffect(() => {
    const interval = setInterval(() => {
      setOrders((prevOrders) => [...prevOrders]);
    }, 5000);
    return () => clearInterval(interval);
  }, []);

  // Function to create a new order
  const createOrder = () => {
    if (customerName.trim() && quantity.trim() && !isNaN(quantity) && Number(quantity) > 0 && Array.isArray(selectedItems) && selectedItems.length > 0) {
      setOrders((prevOrders) => [
        ...prevOrders,
        { id: prevOrders.length + 1, customer: customerName, details: `${quantity} x ${selectedItems.join(", ")}`, status: "Pending" },
      ]);
      // Clear input fields after submission
      setCustomerName("");
      setQuantity("");
      setSelectedItems([]);
    }
  };

  // Function to update the status of an order
  const updateStatus = (id, newStatus) => {
    setOrders((prevOrders) =>
      prevOrders.map((order) => (order.id === id ? { ...order, status: newStatus } : order))
    );
  };

  return (
    <div className="p-6 max-w-lg mx-auto">
      <h1 className="text-2xl font-bold text-center mb-6">Mount Pure Order Tracking</h1>
      <div className="bg-white shadow-lg p-4 rounded-lg mb-6">
        <h2 className="text-lg font-semibold mb-2">Create Your Order</h2>
        {/* Input for customer name */}
        <Input 
          value={customerName} 
          onChange={(e) => setCustomerName(e.target.value)} 
          placeholder="Your Name" 
          className="mb-3"
        />
        {/* Dropdown to select multiple items */}
        <Select value={selectedItems} onValueChange={(value) => setSelectedItems(Array.isArray(value) ? value : [value])}>
          <SelectTrigger className="mb-3">{selectedItems.length > 0 ? selectedItems.join(", ") : "Select items"}</SelectTrigger>
          <SelectContent>
            {availableItems.map((item, index) => (
              <SelectItem key={index} value={item}>{item}</SelectItem>
            ))}
          </SelectContent>
        </Select>
        {/* Input for quantity, restrict to positive numbers */}
        <Input 
          value={quantity} 
          onChange={(e) => setQuantity(e.target.value.replace(/[^0-9]/g, ""))} 
          type="number" 
          placeholder="Quantity" 
          className="mb-3"
        />
        {/* Button to submit order */}
        <Button onClick={createOrder} className="w-full">📦 Submit Order</Button>
      </div>
      <h2 className="text-lg font-semibold mb-3">Your Orders</h2>
      <div className="grid gap-4">
        {/* Display orders or a message if no orders are placed */}
        {orders.length === 0 ? (
          <p className="text-gray-500 text-center">No orders placed yet.</p>
        ) : (
          orders.map((order) => (
            <Card key={order.id} className="shadow-md">
              <CardContent className="flex justify-between p-4 items-center">
                <div>
                  <p className="font-semibold">{order.customer}</p>
                  <p className="text-sm text-gray-600">📌 Order: {order.details}</p>
                  {/* Display order status */}
                  <Badge className="mt-1" variant={order.status === "Delivered" ? "success" : "secondary"}>{order.status}</Badge>
                </div>
                <div className="flex gap-2">
                  {/* Allow updating status to 'In Transit' */}
                  {order.status !== "Delivered" && (
                    <Button onClick={() => updateStatus(order.id, "In Transit")} variant="outline">🚚 In Transit</Button>
                  )}
                  {/* Allow updating status to 'Delivered' */}
                  {order.status !== "Delivered" && (
                    <Button onClick={() => updateStatus(order.id, "Delivered")} variant="success">✅ Delivered</Button>
                  )}
                </div>
              </CardContent>
            </Card>
          ))
        )}
      </div>
    </div>
  );
}
