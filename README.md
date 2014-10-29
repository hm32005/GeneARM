Execution Instructions:
1) Place the training data in the trainData folder.
2) Change configuration file in conf folder as desired.
3) Create a template file and place it in the templates folder.
4) Run ReadIn.java
5) Run MineFrequentItemSets.java
6) Run either AssociationRuleMiner.java or TemplatedAssociationRuleMiner.java based on whether the output without or with the application of templates is desired, respectively. The counts at each level is displayed as the console output and the assoication rules are written to a file as described in the Source Files section.

Folder Structure:

	root
	|------>code
		|----->conf (contains configuration file)
		|----->src (contains source code)
		|----->templates (contains templates)
		|----->trainData (contains training data)
	|------>report
		|----->Report.pdf
	|------>readme.txt

Training Data:

The training data (association-rule-test-data.txt), must be located in the trainData folder.

Configurations:

The file config.properties in the conf folder contains the configurations needed for the algorithm to run. Specifically, the minimum support, minimum confidence, the training file name (trainData/<training file name>) and the template file name (templates/<template file name>) must be configured for appropriate situations. This file additionally has an attribute named ITEMSEP which is specific to our code (not configurable) and need not be changed.

Templates:

The template files must be located in the templates folder. The naming convention is: template_<template number>.txt where template number could be 0, 1, 2, and so on. Association rules generated from the template would have this number as its suffix.

Rules for templates:

We have modified the template syntaces very slightly to make parsing the templates easier. Also, we do NOT allow any parantheses in the templates which creates a further restriction that templates of the third type can only have Left-to-Right precedence. Care must be taken to rewrite the templates in the format compatible with our code in order for proper execution. A detailed example is mentioned below:

Example templates are as follows:

Template 1:

HEAD HAS 2 OF 1_UP,36_DOWN
RULE HAS NONE OF 63_UP,72_DOWN
BODY HAS ANY OF 59_UP,96_DOWN,82_DOWN

Template 2:

HEAD >= 2
BODY >= 3
RULE >= 5

Template 3:

HEAD HAS 2 OF 1_UP,36_DOWN AND BODY >= 3

BODY HAS ANY OF 59_UP,96_DOWN,82_DOWN OR HEAD >= 2 AND RULE HAS NONE OF 63_UP,72_DOWN

is equivalent the template:

(BODY HAS (ANY) OF (59_UP,96_DOWN,82_DOWN) OR SizeOf(HEAD) >= 2) AND (RULE HAS (NONE) OF (63_UP,72_DOWN))

If we want the template:

(BODY HAS (ANY) OF (59_UP,96_DOWN,82_DOWN)) OR (SizeOf(HEAD) >= 2 AND RULE HAS (NONE) OF (63_UP,72_DOWN))

we need to rewrite it as:

HEAD >= 2 AND RULE HAS NONE OF 63_UP,72_DOWN OR BODY HAS ANY OF 59_UP,96_DOWN,82_DOWN

Source Files:

The whole source code is located in the src folder. The folder util within src is the utility package and contains classes whose functionality is shared among the other classes. Specific details about what each file does is mentioned below:

ReadIn.java --> Reads in the training data and creates a parsed version of the data set that is readable by our code. This dataset is stored in dataset.txt and is needed in frequent itemset mining for candidate set pruning. Additionally, the read in code itself generates a file containing all the singletons (unique items in the data set) and stores it in singletons.txt.

MineFrequentItemSets.java --> Implements the Apriori algorithm for candidate generation using self join, pruning and counting based on the configured minimum support. Its output is a file of the format frequentItemSets_<minimum support>.txt. This file is then used by either AssociationRuleMiner.java or TemplatedAssociationRuleMiner.java to mine for association rules.

AssociationRuleMiner.java --> Generic implementation of Apriori algorithm for Assocication Rule Mining that starts from level 0 (null set in head) to level n (null set in body), where n is the size of the rule, pruning branches where the head is infrequent. Its output is a file of the format associationRules_<minimum support>_<minimum confidence>.txt

TemplatedAssociationRuleMiner.java --> Extended version of AssociationRuleMiner.java that uses templates. The code first parses the template file configured in the config file and uses them to build a list of "Conditions" objects. In the counting step, each rule is validated against the list of conditions and is discared if it does not match the template. If it matches the template, the rule would need to have a confidence greater than the threshold in order to be included in the output. Its output is a file of the format associationRules_<minimum support>_<minimum confidence>_<template number>.txt

Conditions.java --> POJO consisting of the basic elements of each template. For template 1 and 2, a single Conditions object is generated. For template 3, the number of Conditions objects generated is equal to the number of atomic templates.

KeyComparator --> Comparator class to compare two genes, for example, 1_UP and 10_UP and return their appropriate sort order. This is used in sorting items within the itemsets in ascending order.

PropertyReader --> Utility class to read in the configuration file and create a Properties object from it.
