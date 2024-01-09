# pandas-challenge-1

# module 4 Challenge

This challenge is to explore a client database for the the client with the highest total profits.

## Installation

first import the pandas as pd and import the client_dataset.csv file.

```bash
import pandas as pd
df = pd.read_csv('Resources/client_dataset.csv')
```
## Usage

```python
## Use the describe function to gather some basic statistics
statistics = df.describe()
print(statistics)

## What three item categories had the most entries?
df["category"].value_counts().head(3)

#  For the category with the most entries,
# which subcategory had the most entries?
df.loc[df["category"] == "consumables", "subcategory"].value_counts().head(1)

# Which five clients had the most entries in the data?
df['client_id'].value_counts().head(5)

# Store the client ids of those top 5 clients in a list.
top_5_clients = list(df["client_id"].value_counts().head(5).index)
top_5_clients

# How many total units (the qty column) did the
# client with the most entries order order?
df.loc[df["client_id"] == top_5_clients[0], "qty"].sum()

# Create a column that calculates the 
# subtotal for each line using the unit_price
# and the qty

df["line_subtotal"] = df["unit_price"] * df["qty"]
df[['unit_price', 'qty', 'line_subtotal']].head(2)

# Create a column for shipping price.
# Assume a shipping price of $7 per pound
# for orders over 50 pounds and $10 per
# pound for items 50 pounds or under.

def shipping_calculator(weight):
    if weight > 50:
        return weight * 7
    return weight * 10

df['total_weight'] = df['qty'] * df['unit_weight']
df['shipping_price'] = df['total_weight'].apply(shipping_calculator)
df[['unit_price', 'unit_weight', 'qty', 'total_weight', 'shipping_price']].head(3)

# Create a column for the total price
# using the subtotal and the shipping price
# along with a sales tax of 9.25%
df["line_price"] = round((df["line_subtotal"] + df["shipping_price"]) * 1.0925, 2)
df[['line_subtotal', 'shipping_price', 'line_price']].head(3) 

# Create a column for the cost
# of each line using unit cost, qty, and
# shipping price (assume the shipping cost
# is exactly what is charged to the client).
df['line_cost'] = df['unit_cost'] * df['qty'] + df['shipping_price']
df.head(3)

# Create a column for the profit of
# each line using line cost and line price
df["line_profit"] = df["line_price"] - df["line_cost"]
df.head(3)

# Check your work using the totals above

order_ids = [2742071, 2173913, 6128929]

def find_order_price(order_id):
    order_lines_df = df.loc[df['order_id'] == order_id, 'line_price']
    return round(order_lines_df.sum(), 2)

for order_id in order_ids:
    print(f"Order {order_id} total: ${find_order_price(order_id)}")

# How much did each of the top 5 clients by quantity
# spend? Check your work from Part 1 for client ids.

def find_client_id(client_id,column):
    client_df = df.loc[df['client_id'] == client_id, column]
    return round(client_df.sum(), 2)

for client_id in top_5_clients:
    print(f"{client_id} : ${find_client_id(client_id, 'line_price')}")

# Create a summary DataFrame showing the totals for the
# for the top 5 clients with the following information:
# total units purchased, total shipping price,
# total revenue, and total profit. Sort by total profit.

# Select the top 5 clients
top_5_clients_df = df[df["client_id"].isin(top_5_clients)]

# Group by client id
top_5_clients_grouped = top_5_clients_df.groupby("client_id")
top_5_clients_grouped = top_5_clients_grouped.agg({ "qty": "sum", "shipping_price": "sum", "line_price": "sum", "line_profit": "sum" })
top_5_clients_grouped = top_5_clients_grouped.sort_values("line_profit", ascending=False)   
top_5_clients_grouped.head(5)

# Format the data and rename the columns
# to names suitable for presentation.
# Currency should be in millions of dollars.

df = df.rename(columns={"qty": "Total Units Purchased", "shipping_price": "Total Shipping Price", "line_price": "Total Revenue", "line_profit": "Total Profit"})
df["Total Shipping Price"] = df["Total Shipping Price"].map("${:,.2f}".format)  
df["Total Revenue"] = df["Total Revenue"].map("${:,.2f}".format)
df["Total Profit"] = df["Total Profit"].map("${:,.2f}".format)
df["Total Units Purchased"] = df["Total Units Purchased"].map("{:,}".format)
df.head(5)

# Sort the updated data by "Total Profit" from highest to lowest
df = df.sort_values("Total Profit", ascending=False)
df.head(30) 
```

## Contributing

I received tutoring from Thabo Charles and Mohammed Fauwaaz
I also used the ask an expert.


Pull requests are welcome. For major changes, please open an issue first
to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

                    GNU GENERAL PUBLIC LICENSE
                       Version 3, 29 June 2007
