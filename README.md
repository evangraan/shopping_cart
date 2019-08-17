# 1

## Running

You can run the system so:



You can run the test suite so, from the project root directory:

behave 

## 1.1

* [Link](link.md)

## Design
Aggregation over containment. The basket class does not contain a catalogue or offers, rather these are provided to it across its public API
After I had specced the system using the BDD suite (usually this would be limited to the highest value feature, which I'd then implement, and so iterate, but this project is small enough to spec in one go), I decided on the following architecture for the system:

- json for the data store (a simple file)). json is often a great choice, and as data can be sourced from databases (which in a microservices world often sit behind APIs) or diretly from APIs, json is a safe, flexible choice.
- given the test direction to focus on the calculation core rather, I included a simple configuration for specifying the catalogue data source fule in the project. I did how-ever include a check whether a .reload file is present, which would load a new catalogue if present and then delete the .reload signal. This is to allow for change of catalogue without restarting the system.
- Using MVC is always a good idea for systems that have output and models (the rules in this case) and so I implemented a very simple command line controller and stdout view.
- To avoid over- and under-design I would normally ask the client what success from their perspective looks like and try and elicit some non functional requirements to guide the selection of design components. From the test instructions I extracted the NFR 'focus on a solid core algorithm' and so I made the rest really simple, but I did sketch out some basic design components as indicated above so that it is easy to grow and maintain the system without too much refactor.
- shopping carts are usually related to sessions (which can live long)- as such I made the call to construct a basket with the offers and catalogue to use. This could easily be refactored at the API level though if this call was not appropriate for the use case.

# Testing

I added some tests for features I found useful in building the solution, such as insert, remove, list. These can be ignored as they were not part of the core focus of the excercise, but they were useful in facilitating me testing an builing the core, so I've left them in.

On projects that use BDD through all layers of the stack I would normally reserve unit testing only for TDD purposes or core, complex algorithms and let BDD drive coverage. In cases where BDD is exclusively at the UA or integration level, I would use unit tests more prolifically. On this project then I think BDD suffices and I did not include unit tests.

I did how-ever add the test suit to my personal Jenkins, and so this project can be added to a CI/CD pipeline. You can see it running here:

https://host.3sds.co.uk:8080
username: admin
password: kDFmL?z2%,'CPjAy

Please note there are other personal project on this system, please only access the ecs.co.uk project :)
I'll change the password in a couple of days. Its not a critical system though, I do not as a rule share admin passwords.

## Notes

I would recommend to the retailer that they standardize on one term for items (either product or item.) In my implementation I used item consistently, and in 'catalogue.feature' indicated that Product is a synonym.

In the case of data itegrity issues in the catalogue, I made the decision to load all valid items, but to report an error as well.

I was not sure if you intended the *** pre- and post-fixes to the item name to be included. I apologize if it was intended as part of the name, usually I'd ask the client. In my data set I did not include the ***s.

## Discount algorithm

I used a simple Chain of Responsibility design to process discount offers using the basket. Each offer returns the savings it can apply, and returns the basket items list, now augmented with the offer identifier tagged onto each item that was part of the set of items that matched the offer.

Once the offers have all been processed, the items are traversed to see which items have more than one offer attached to it. For these, the highest offer saving is selected, and the other competing offer tag(s) removed from all items in the basket. The multiple offer check is then repeated until no product has more than one offer attached to it.

## Bonus questions

I suspect the bonus problems lead to an NP hard set of requirements, which would lead to some complex code. I unfortunately do not have enough disposable time at the moment to implement a solution for it, though a solution would probably consist of finding an optimal order in which to iterate over a set of matches. E.g. Tag all items in the basket with a list of orders that match it, then look according to some rule for an order in which to apply compound discounts, and do this for each product that had discounts applied for it. Where a conflict rule set finds a match, calculate basket discounts and resolve the conflict by selecting the best one. One could consider a forward chaining (reasoning) algorithm perhaps. Or perhaps a much simpler solution exits. Apologies for not engaging the bonus questions at this time.

## Development approach

This excercise was build test-first, specifically using the 'behave' BDD framework. Tests were written without any implementation code existing, after which the code was implemented using TDD (red-green-refactor).

All dependencies can be installed by running TBD.

If you want to install behave manually, please issue the command:

pip install behave

All BDD tests live in the 'features' directory.

All BDD test steps live in 'features/steps'.

## Development environment

This excercise was completed on an AWS reserved instance that I own running Ubuntu 18.04 (bionic). If you'd like to setup a similar environment, it is easy to do so:

Spin up an instance from the AWS marketplace

sudo apt-get install python3.7

sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.7 1

git clone https://github.com/evangraan/ecs.co.uk.git

# Todo

- convert tabs to 4 spaces in all files
- visually inspect should be automated
- dependency script (shell? python tool?)
- describe json change required
- python coding conventions
