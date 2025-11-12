# Data, Calculations, and Actions

### **Functional Thinking Overview**

-   Functional programming emphasizes **data**, **calculations**, and **actions**.
-   Understanding these distinctions helps create cleaner, more testable, and reliable software.


## **1. Actions**

**Definition:** An instruction where time and order matter --- causes a **side effect**.

**Examples:** - Printing to screen - Sending an email - Updating a database - Changing a global or instance variable

**Key Points:** - Also called **impure functions**. - Must be handled carefully because they can lead to unpredictable behavior.

**Techniques for Managing Actions:** - Safely change state over time. - Guarantee ordering. - Ensure actions happen only once.


## **2. Data**

**Definition:** Facts about events --- data *does no work*.

**Examples:** - Variables, arrays, lists, objects.

**Immutable vs Mutable:** - **Immutable data:** Cannot be changed (preferred in FP). - **Mutable data:** Can be changed.

**Techniques for Data:** - Organize for efficient access. - Use clear principles for representing key facts.


## **3. Calculations**

**Definition:** Computations from input to output with **no side effects**.

**Key Features:** - Same input → same output (deterministic). - Also called **pure functions**. - Operate only on data and return new data.

**Examples:** - Finding a max value in a list. - Checking if an email address is valid.

**Benefits:** - Easier to test and reuse. - Composable due to **referential transparency**.

## **Referential Transparency**

A function is **referentially transparent** if you can replace its call with its return value **without changing program behavior**.

✅ Referentially transparent example:

``` java
public static double hypotenuse1(double a, double b) {
    return Math.sqrt(a*a + b*b);
}
```

❌ Not referentially transparent:

``` java
public static double hypotenuse2(double a, double b) {
    var h = Math.sqrt(a*a + b*b);
    System.out.println(h);
    return h;
}
```


## **Function Composition**

-   Combining pure functions together.
-   Works easily because of **referential transparency**.

**Benefits of Calculations:** - Composable. - Easier to analyze and test. - IDEs can reason about them.


## **Functional Core, Imperative Shell**

  **Functional Core** - Immutable data + pure functions. Application logic lives here.

  **Imperative Shell** - Coordinates external interactions (I/O, DB, files). Controls sequencing.
 
## **Recognizing Data, Calculations, and Actions**


  **Data**  ->  What does my program need to know?    ->  Facts only.

  **Calculation**   ->  What does my program need to decide?   ->  Pure logic.

  **Action**     ->  When and in what order should things happen?  ->  Has side effects.                                


## **Example: CouponDog**

-   Sends weekly coupon emails.
-   Subscribers with \>10 referrals get better coupons.

Functional approach: 
1. **Data:** Subscriber info, referral count, coupon data.
2. **Calculations:** Determine coupon rank, prepare email list.
3. **Actions:** Actually send the emails.

Separation makes it easier to test calculations independently of
actions.



## **Using Records and Enums for Data**

### **Records**

Immutable data holders.

``` java
record Subscriber(String email, int recommendationCount) {}
```

### **Enums**

Fixed set of values, type-safe.

``` java
enum CouponRank { GOOD, BEST, BAD }
record Coupon(String code, CouponRank rank) {}
```


## **The Problem with Actions**

Actions are **contagious** --- any function that calls an action is also an action.

``` java
public static void calcPayout(Affiliate affiliate) {
    var owed = affiliate.sales() * affiliate.commission();
    if (owed > 100) {
        sendPayout(affiliate.bankCode(), owed); // side effect
    }
}
```

Since `calcPayout` calls `sendPayout`, it's also an action. And anything calling `calcPayout` becomes one too.


## **Function Inputs and Outputs**

- **Explicit Inputs**  = Parameters
- **Explicit Outputs**  = Return values
- **Implicit Inputs**  = Reading globals, external files
- **Implicit Outputs** = Modifying globals, printing, writing files

> Any implicit input or output makes a function an **action**.


## **Extracting Calculations from Actions**

### Steps

1.  Identify calculation part.
2.  Extract it into a new function.
3.  Replace implicit inputs/outputs with parameters and return values.

Example:

``` java
public static double calcTotal(List<CartItem> cart) {
    var total = 0.0;
    for (var item : cart) total += item.price();
    return total;
}
```

Then use it inside the action:

``` java
public static void calcCartTotal() {
    shoppingCartTotal = calcTotal(shoppingCart);
    updateShippingIcons();
}
```


## **Converting Implicit I/O to Explicit**

Example improvement:

``` java
public static List<CartItem> addItem(String name, double price, List<CartItem> cart) {
    var newCart = new ArrayList<>(cart);
    newCart.add(new CartItem(name, price));
    return newCart;
}
```

Now `addItem` is a pure calculation.


## **Reducing Implicit Inputs and Outputs**

-   Makes code easier to test.
-   Reduces bugs from global state.
-   Even actions become cleaner if they use explicit inputs/outputs.


## **Practical Takeaways**

1.  **Prefer Data \> Calculations \> Actions**
2.  **Separate functional core from imperative shell**
3.  **Extract pure functions where possible**
4.  **Use immutability and explicit I/O**


