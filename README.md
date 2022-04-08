# B2C-Project

Contents

1. Mini world and Project Description

2. Basic structure

3. Functional requirements

4. Entity Relation (ER) diagram and constraints

5. Relational database schema

6. Implementation

7. Creating tables

8. Inserting data

9. Queries

10. Basic queries

11. Trigger function

 Mini world and Description
 
In this modern era of online shopping no seller wants to be left behind, moreover due to its simplicity the shift from offline selling model to an online selling model is witnessing a rampant growth.
Therefore, as an engineer our job is to ease the path of this transition for the seller. Amongst many things that an online site requires the most important is a database system. Hence in this project we are planning to design a database where small clothing sellers can sell their product online.

Basic Structure
2.1 Functional requirement

A new user can register on the website.

A customer can see details of the product present in the cart

A customer can view his order history.

Admin can start a sale with certain discount on every product.

Customer can filter the product based on the product details.

A customer can add or delete a product from the cart.

A seller can unregister/ stop selling his product.

A seller/ customer can update his details.

Admin can view the products purchased on particular date.

Admin can view number of products sold on a particular date.

A customer can view the total price of product present in the cart unpurchased.

Admin can view details of customer who have not purchased anything.

Admin can view total profit earned from the website.


2.2 Entity Relation Diagram
ER diagram
![new_er](https://user-images.githubusercontent.com/72606774/162399970-aa8e0583-fbfd-4414-8100-accc3779d235.png)


3. Implementation

create database B2C_project;
use B2C_project;

CREATE TABLE Cart
    (
        Cart_id VARCHAR(7) NOT NULL,
        PRIMARY KEY(Cart_id)
    );

    CREATE TABLE Customer
    (
        Customer_id VARCHAR(6) NOT NULL,
        c_pass VARCHAR(10) NOT NULL,
        Name VARCHAR(20) NOT NULL,
        Address VARCHAR(20) NOT NULL,
        Pincode INT(6) NOT NULL,
        Phone_number_s INT(10) NOT NULL,
        PRIMARY KEY (Customer_id),
        Cart_id VARCHAR(7) NOT NULL,
        FOREIGN KEY(Cart_id) REFERENCES cart(Cart_id)
    );

    CREATE TABLE Seller
    (
        Seller_id VARCHAR(6) NOT NULL,
        s_pass VARCHAR(10) NOT NULL,
        Name VARCHAR(20) NOT NULL,
        Address VARCHAR(10) NOT NULL,
        PRIMARY KEY (Seller_id)
    );

    CREATE TABLE Seller_Phone_num
    (
        Phone_num INT(11) NOT NULL,
        Seller_id VARCHAR(6) NOT NULL,
        PRIMARY KEY (Phone_num, Seller_id),
        FOREIGN KEY (Seller_id) REFERENCES Seller(Seller_id)
        ON DELETE CASCADE
    );

    CREATE TABLE Payment
    (
        payment_id VARCHAR(7) NOT NULL,
        payment_date DATE NOT NULL,
        Payment_type VARCHAR(10) NOT NULL,
        Customer_id VARCHAR(6) NOT NULL,
        Cart_id VARCHAR(7) NOT NULL,
        PRIMARY KEY (payment_id),
        FOREIGN KEY (Customer_id) REFERENCES Customer(Customer_id),
        FOREIGN KEY (Cart_id) REFERENCES Cart(Cart_id),
        total_amount numeric(6)
    );

    CREATE TABLE Product
    (
        Product_id VARCHAR(7) NOT NULL,
        Type VARCHAR(7) NOT NULL,
        Color VARCHAR(15) NOT NULL,
        P_Size VARCHAR(2) NOT NULL,
        Gender CHAR(1) NOT NULL,
        Commission INT(2) NOT NULL,
        Cost INT(5) NOT NULL,
        Quantity INT(2) NOT NULL,
        Seller_id VARCHAR(6),
        PRIMARY KEY (Product_id),
        FOREIGN KEY (Seller_id) REFERENCES Seller(Seller_id)
        ON DELETE SET NULL
    );

    CREATE TABLE Cart_item
    (
        Quantity_wished INT(1) NOT NULL,
        Date_Added DATE NOT NULL,
        Cart_id VARCHAR(7) NOT NULL,
        Product_id VARCHAR(7) NOT NULL,
        FOREIGN KEY (Cart_id) REFERENCES Cart(Cart_id),
        FOREIGN KEY (Product_id) REFERENCES Product(Product_id),
        Primary key(Cart_id,Product_id)
    );

    alter table Cart_item add purchased varchar(3) default 'NO';
    
     insert into Cart values('crt1011');

    insert into Customer values('cid100','ABCM1235','rajat','G-453','632014',98931358, 'crt1011');

    insert into Seller values('sid100','12345','aman','delhi cmc');

    insert into Product values('pid1001','jeans','red','32','M',10,10005,20,'sid100');

    insert into Seller_Phone_num values(99436206,'sid100');

    insert into Cart_item values(3,'2019-03-02','crt1011','pid1001','Y');

    insert into Payment values('pmt1001','2020-06-02','online','cid100','crt1011',NULL);
    
     select * from product where product_id in(
        select product_id from Cart_item where (Cart_id in (
            select Cart_id from Customer where Customer_id='cid100'
        )));
        
        
        4.3 Triggers
Trigger that will execute before inserting new customer to database and inserting a new cartId to the cart_items table


create or replace function numCartId(cd in varchar)

    return number
    is
    total number(2):=0;
    begin
    select count(*) into total
    from cart_item
    where cart_id=cd;
    return total;
    end;
    Trigger
    Create or replace trigger before_customer
    before insert
    on
    customer
    for each row
    declare
    c varchar(10);
    n number(2);
    begin
    c:= :new.cart_id;
    n:=numCartId(c);
    if n>0 then
    dbms_output.put_line('Sorry');
    end if;
    insert into cart values(c);
    end;
