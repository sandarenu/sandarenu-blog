+++
author = ""
date = "2016-01-22T16:11:49+05:30"
draft = false
image = ""
menu = ""
share = true
comments = true
slug = "understanding-guava-lists-transform"
tags = ["Java", "Programming", "Guava"]
title = "Understanding Guava Lists.transform()"
+++

Guava is a wonderful library by Google. Before Java 8 Guava was one of the best ways to use functional programming in Java. 

I've being using Guava in one of my projects and came accross an strance issue reasontly. I had a list of items and I wanted to convert them to different object and filter some of those items.

After filtering I was changing some values in the list items. But what was strange was thay when I printed the list back it was not showing the changed value. It was still showing the original value. Code would look something similar to below. 


```java
import com.google.common.base.Function;
import com.google.common.collect.Lists;

import java.util.List;

public class LineItemHolderGuava {
    private List<ProcessedItem> mainItems;
    private List<ProcessedItem> optionalItems;

    public LineItemHolderGuava(List<LineItem> main, List<LineItem> optional){
        Function<LineItem, ProcessedItem> toSummaryItem = new Function<LineItem, ProcessedItem>() {
            @Override
            public ProcessedItem apply(LineItem lineItem) {
                System.out.println("Function called with : " + lineItem);
                return new ProcessedItem(lineItem);
            }
        };

        this.optionalItems = Lists.transform(optional, toSummaryItem);
        this.mainItems = Lists.transform(main, toSummaryItem);
    }

    public List<ProcessedItem> getMainItems() {
        return mainItems;
    }

    public List<ProcessedItem> getOptionalItems() {
        return optionalItems;
    }

    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("LineItemHolder{");
        sb.append("mainItems=").append(mainItems);
        sb.append(", optionalItems=").append(optionalItems);
        sb.append('}');
        return sb.toString();
    }
}
```

I have created a simple main method to test this. 

```java
public static void main(String [] args){
        Money m1 = new Money("LKR", "10");
        Money m2 = new Money("LKR", "20");
        Money m3 = new Money("LKR", "30");
        Money m4 = new Money("LKR", "40");

        LineItem l1 = new LineItem(m1, LineItem.OperationType.CREDIT, "L1");
        LineItem l2 = new LineItem(m2, LineItem.OperationType.DEBIT, "L2");
        LineItem l3 = new LineItem(m2, LineItem.OperationType.CREDIT, "L3");
        LineItem l4 = new LineItem(m2, LineItem.OperationType.DEBIT, "L5");

        List<LineItem> list1 = new ArrayList<LineItem>();
        list1.add(l1);
        list1.add(l2);

        List<LineItem> list2 = new ArrayList<LineItem>();
        list2.add(l3);
        list2.add(l4);

        LineItemHolderGuava guavaHolder = new LineItemHolderGuava(list1, list2);
        System.out.println("G 1: " + guavaHolder);

        guavaHolder.getMainItems().get(0).setAmount(new Money("USD", "50"));
        System.out.println("G 2: " + guavaHolder);

        guavaHolder.getMainItems().get(0).getAmount().setCurrencyCode("KES");
        System.out.println("G 3: " + guavaHolder);
    }
```

What do you expect as the output?. I expected to see the second log printed with `USD`and third log printed with `KES`, but I was wrong. I got following output. 

```
G 1: LineItemHolder{mainItems=[ProcessedItem{amount=Money{currencyCode='LKR', amount=10}, description='L1'}, ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L2'}], optionalItems=[ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L3'}, ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L5'}]}

G 2: LineItemHolder{mainItems=[ProcessedItem{amount=Money{currencyCode='LKR', amount=10}, description='L1'}, ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L2'}], optionalItems=[ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L3'}, ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L5'}]}

G 3: LineItemHolder{mainItems=[ProcessedItem{amount=Money{currencyCode='KES', amount=10}, description='L1'}, ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L2'}], optionalItems=[ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L3'}, ProcessedItem{amount=Money{currencyCode='LKR', amount=20}, description='L5'}]}

```

Strangly second log got printed with `LKR` and third log got printed with `KES`. This was driving me nuts. I put logs everywere, and debugged the code and I couldn't find any reason for this. Setter was called properly and I was unable to find a proper reason for this. I ended up rewriting this using Java8 stream API finally. 

```java
import java.util.List;
import java.util.stream.Collectors;

public class LineItemHolderJ8 {
    private List<ProcessedItem> mainItems;
    private List<ProcessedItem> optionalItems;

    public LineItemHolderJ8(List<LineItem> main, List<LineItem> optional) {
        this.optionalItems = main.stream().map(l -> new ProcessedItem(l)).collect(Collectors.toList());
        this.mainItems = main.stream().map(l -> new ProcessedItem(l)).collect(Collectors.toList());
    }

    public List<ProcessedItem> getMainItems() {
        return mainItems;
    }

    public List<ProcessedItem> getOptionalItems() {
        return optionalItems;
    }

    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("LineItemHolder{");
        sb.append("mainItems=").append(mainItems);
        sb.append(", optionalItems=").append(optionalItems);
        sb.append('}');
        return sb.toString();
    }
}
```

During the day I was thinking about this and suddenly I got a feeling that this might be some special thing related to Guava. To my surprise it turn out to be how Guava `Lists.transform` works. When I called ` this.mainItems = Lists.transform(main, toSummaryItem); `, I expected that it will transform the list in to new types and store the converted list in `this.mainItems`. But I was wrong. Guava `Lists.transform` do not transform the object completely at the first time. What is does is when someone access the list, on the fly it transforms and return the objects. 
So each time I call `toString` on the list, Guava will be taking the original list and convert them to the new object types using the function I've given and return that to me. That why setting `guavaHolder.getMainItems().get(0).setAmount(new Money("USD", "50"));` didnt work. Even though we set new `Money` object, when we call the `toString` method to print the list, guava will be creating a completely new list using the original list. 
In the second time we directly changed the currency code `guavaHolder.getMainItems().get(0).getAmount().setCurrencyCode("KES");`. Here we are actually changing the Money object refered by both the original list and converted list. 
This functionaly was explained properly in the [Guava docs](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/collect/Lists.html#transform%28java.util.List,%20com.google.common.base.Function%29). 

> Returns a list that applies function to each element of fromList. The returned list is a transformed view of fromList; changes to fromList will be reflected in the returned list and vice versa.

> Since functions are not reversible, the transform is one-way and new items cannot be stored in the returned list. The add, addAll and set methods are unsupported in the returned list.

> The function is applied lazily, invoked when needed. This is necessary for the returned list to be a view, but it means that the function will be applied many times for bulk operations like List.contains(java.lang.Object) and List.hashCode(). For this to perform well, function should be fast. To avoid lazy evaluation when the returned list doesn't need to be a view, copy the returned list into a new list of your choosing. 

It was my fault that I didn't read the docs properly. I ended up wasting around half a day to fixing a strange bug in one of my code due to this. So next time when you use `Lists.tranform` better remember this.