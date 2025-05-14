def run_supply_chain_app():
    import tkinter as tk
    from tkinter import ttk, messagebox, filedialog
    from datetime import datetime
    import csv

    class Inventory:
        def _init_(self):
            self.stock = {"Sensors": 100, "Steel": 200, "Plastic": 150,"Wood":300,"Iron":400,"Electronic chip":250,"Sandalwood":180,"Charcoal":200,
                         "Timber":100,"Gadgets":500,"Rayon":200,"Marble":400,"Limestone":298}
        def add_stock(self, product, quantity):
            self.stock[product] = self.stock.get(product, 0) + quantity
        def reduce_stock(self, product, quantity):
            if self.stock.get(product, 0) >= quantity:
                self.stock[product] -= quantity
                return True
            return False
        def get_stock(self):
            return dict(self.stock)

    class SupplyChainApp:
        def _init_(self, root):
            self.root = root
            self.root.title("Supply Chain Management System")
            self.inventory = Inventory()
            self.orders = []
            self.suppliers = []
            self.create_widgets()
            self.update_inventory_display()

        def create_widgets(self):
            inventory_frame = tk.LabelFrame(self.root, text="Inventory")
            inventory_frame.grid(row=0, column=0, padx=10, pady=10)
            self.inventory_tree = ttk.Treeview(inventory_frame, columns=("Product", "Quantity"), show="headings")
            self.inventory_tree.heading("Product", text="Product")
            self.inventory_tree.heading("Quantity", text="Quantity")
            self.inventory_tree.pack()

            order_frame = tk.LabelFrame(self.root, text="Place Order")
            order_frame.grid(row=1, column=0, padx=10, pady=10)
            tk.Label(order_frame, text="Product:").grid(row=0, column=0)
            self.product_var = tk.StringVar()
            self.product_menu = ttk.Combobox(order_frame, textvariable=self.product_var, state="readonly")
            self.product_menu["values"] = list(self.inventory.get_stock().keys())
            self.product_menu.grid(row=0, column=1)

            tk.Label(order_frame, text="Quantity:").grid(row=1, column=0)
            self.qty_var = tk.IntVar(value=1)
            tk.Entry(order_frame, textvariable=self.qty_var).grid(row=1, column=1)
            tk.Button(order_frame, text="Submit Order", command=self.place_order).grid(row=2, column=0, columnspan=2)

            restock_frame = tk.LabelFrame(self.root, text="Restock Inventory")
            restock_frame.grid(row=2, column=0, padx=10, pady=10)
            tk.Label(restock_frame, text="Product:").grid(row=0, column=0)
            self.restock_product = tk.StringVar()
            self.restock_menu = ttk.Combobox(restock_frame, textvariable=self.restock_product, state="readonly")
            self.restock_menu["values"] = list(self.inventory.get_stock().keys())
            self.restock_menu.grid(row=0, column=1)

            tk.Label(restock_frame, text="Quantity:").grid(row=1, column=0)
            self.restock_qty = tk.IntVar()
            tk.Entry(restock_frame, textvariable=self.restock_qty).grid(row=1, column=1)
            tk.Button(restock_frame, text="Restock", command=self.restock_inventory).grid(row=2, column=0, columnspan=2)

            supplier_frame = tk.LabelFrame(self.root, text="Supplier Management")
            supplier_frame.grid(row=3, column=0, padx=10, pady=10)
            tk.Label(supplier_frame, text="Supplier Name:").grid(row=0, column=0)
            self.supplier_name = tk.StringVar()
            tk.Entry(supplier_frame, textvariable=self.supplier_name).grid(row=0, column=1)
            tk.Label(supplier_frame, text="Supplies:").grid(row=1, column=0)
            self.supplier_product = tk.StringVar()
            tk.Entry(supplier_frame, textvariable=self.supplier_product).grid(row=1, column=1)
            tk.Button(supplier_frame, text="Add Supplier", command=self.add_supplier).grid(row=2, column=0, columnspan=2)
            self.supplier_listbox = tk.Listbox(supplier_frame, width=40)
            self.supplier_listbox.grid(row=3, column=0, columnspan=2)

            orders_frame = tk.LabelFrame(self.root, text="Order History")
            orders_frame.grid(row=0, column=1, rowspan=4, padx=10, pady=10)
            self.orders_tree = ttk.Treeview(orders_frame, columns=("Time", "Product", "Quantity", "Status"), show="headings")
            for col in ("Time", "Product", "Quantity", "Status"):
                self.orders_tree.heading(col, text=col)
            self.orders_tree.pack()
            tk.Button(orders_frame, text="Export Orders to CSV", command=self.export_orders).pack(pady=5)

        def update_inventory_display(self):
            self.inventory_tree.delete(*self.inventory_tree.get_children())
            for product, qty in self.inventory.get_stock().items():
                self.inventory_tree.insert("", "end", values=(product, qty))
            self.product_menu["values"] = list(self.inventory.get_stock().keys())
            self.restock_menu["values"] = list(self.inventory.get_stock().keys())

        def update_orders_display(self):
            self.orders_tree.delete(*self.orders_tree.get_children())
            for order in self.orders:
                self.orders_tree.insert("", "end", values=order)

        def place_order(self):
            product = self.product_var.get()
            try:
                qty = int(self.qty_var.get())
            except ValueError:
                messagebox.showerror("Invalid Input", "Quantity must be an integer.")
                return
            if not product or qty <= 0:
                messagebox.showwarning("Invalid Input", "Enter valid product and quantity.")
                return
            status = "Fulfilled" if self.inventory.reduce_stock(product, qty) else "Backordered"
            timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            self.orders.insert(0, (timestamp, product, qty, status))
            self.update_inventory_display()
            self.update_orders_display()
            messagebox.showinfo("Order Submitted", f"Order Status: {status}")

        def restock_inventory(self):
            product = self.restock_product.get()
            try:
                qty = int(self.restock_qty.get())
            except ValueError:
                messagebox.showerror("Invalid Input", "Quantity must be an integer.")
                return
            if not product or qty <= 0:
                messagebox.showwarning("Invalid Input", "Enter valid product and quantity.")
                return
            self.inventory.add_stock(product, qty)
            self.update_inventory_display()
            messagebox.showinfo("Inventory Updated", f"{qty} units added to {product}.")

        def add_supplier(self):
            name = self.supplier_name.get().strip()
            supply = self.supplier_product.get().strip()
            if not name or not supply:
                messagebox.showwarning("Invalid Input", "Please fill in both fields.")
                return
            entry = f"{name} supplies {supply}"
            self.suppliers.append(entry)
            self.supplier_listbox.insert(tk.END, entry)
            self.supplier_name.set("")
            self.supplier_product.set("")

        def export_orders(self):
            if not self.orders:
                messagebox.showwarning("No Orders", "There are no orders to export.")
                return
            file_path = filedialog.asksaveasfilename(defaultextension=".csv", filetypes=[("CSV Files", "*.csv")])
            if file_path:
                try:
                    with open(file_path, "w", newline="") as csvfile:
                        writer = csv.writer(csvfile)
                        writer.writerow(["Time", "Product", "Quantity", "Status"])
                        writer.writerows(self.orders)
                    messagebox.showinfo("Export Successful", f"Orders exported to {file_path}")
                except Exception as e:
                    messagebox.showerror("Export Failed", str(e))

    root = tk.Tk()
    app = SupplyChainApp(root)
    root.mainloop()

# Run the GUI
run_supply_chain_app()
