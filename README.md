# A Framework for System Design

## Step 1 - Understanding the Problem and Defining Scope

Before jumping to designing your solution, stop and define together with the interviewer what the scope of the problem is.

- What features are we building for our product?
- How many users does the product have?
- How fast do we expect the company to grow? In 3 months? 6 months? a year?
- What are the already-in-place tools the company has that we can use?

## Step 2 - Proposing a High-level design

In this step, we design a higher-level, broader design which doesn't focus in the specifics but aims to get the interviewer's approval before diving in.

- Come up with the initial blueprint.
- Treat the interviewer as a teammate of sorts, constantly ask them questions and feedback.

- Draw box diagrams with key components on the whiteboard/paper. This might include tiers such as user, web, data, cache, CDN, message queue, and even APIs.
- Ask the interviewer whether a back-of-the-envelope estimations is necessary to determine whether the blueprint fits the technical requirements.

## Step 3 - Design Deep Dive

At this point the following goals need to have been achieved:

- You and the interviwer agreed on the overall goals and features of the product.
- The high-level blueprint of the product has been finished, with the interviewer's approval.
- Some ideas of where to begin the deep dive have been given.

Deep diving is about finding points of interest in the design and expanding upon them.

## Step 4 - Wrap Up

In this final step, we recap all that has been presented and some topics such as:

- Error cases that might happen (e.g., server failure and network loss)
- Possible further improvements and bottlenecks.
