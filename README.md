Here's a detailed breakdown of Supply Chain Management System Python program:

✅ Features of the Program

1)Inventory Management

 i)View current stock levels.

 ii)Add stock (Restock feature).

 iii)Reduce stock when orders are placed.

2)Order Placement

 i)Place orders for specific products and quantities.

 ii)Automatically updates inventory levels.

 iii)Displays order status: Fulfilled (if enough stock) or Backordered (if stock is insufficient).

 iv)Maintains and displays a history of all orders.

3)Supplier Management

  i)Add suppliers along with the products they supply.

  ii)Display a list of all added suppliers.

4)Export Orders

  i)Export order history to a CSV file.

5)User Interface

  i)Graphical User Interface (GUI) built using tkinter.

  ii)Organized layout with labeled sections for each functionality.

🛠️ Technologies Used
Technology	Purpose
  1)Python	Main: programming language
  
  2)Tkinter:For creating the graphical user interface (GUI)
  
  3)ttk (Themed Widgets):For styled GUI components like Treeview, Combobox
  
  4)messagebox & filedialog:To show alerts and save file dialogs
  
  5)datetime:To timestamp orders
  
  6)csv:To export orders to a CSV file

⚙️ How It Works (Logic and Flow)
 1)Startup

  i)run_supply_chain_app() creates the main window and initializes the app.

  ii)SupplyChainApp class sets up inventory, suppliers, orders, and GUI widgets.

 2)Inventory System

Inventory class manages a dictionary of products and quantities.

Provides add_stock(), reduce_stock(), and get_stock() methods.

 3)Placing Orders

  i)User selects a product and enters quantity.

  ii)On submit:

   The app checks if stock is enough.

  iii)Reduces stock if available.

  iv)Adds the order to history with time, product, quantity, and status.

 4)Restocking Inventory

  i)User selects a product and quantity to add.

  ii)Updates the inventory.

 5)Managing Suppliers

  i)User inputs a supplier name and the product they supply.

  ii)Adds this information to a list and displays it.

 6)Order Export

  i)User clicks “Export Orders to CSV”.

  ii)Opens file save dialog.

  iii)Writes all order data to a .csv file.
