#Fancytree

[Fancytree](https://github.com/mar10/fancytree)  is a tree plugin for jQuery with support for persistence, keyboard, check-boxes, tables (grid), drag'n'drop, and lazy loading.

The tree data is stored in an array of nested objects. For example



    source: [
    {title: "Node 1", key: "1"},
    {title: "Folder 2", key: "2", folder: true, children:               [    
      {title: "Node 2.1", key: "3"},
      {title: "Node 2.2", key: "4"}
      ]}
    ],
    ...
  
A good [documentation](http://wwwendt.de/tech/fancytree/demo/index.html) can be found here.




Actions performed on a FancyTree
--------------------------------

We need to be able to perform the following tasks on a FancyTree

 - create a folder
 - rename a folder
 - move a folder
 - delete a folder

These actions need to be separated from the general code flow, an abstraction is clearly asked for!

Achieving theses tasks is a three step process:

 1. deconstruct tree
 2. perform action
 3. reconstruct tree

The first and the third step will be identical for all actions, no modification is needed. You will only have to adapt the second step!
Now that the three step process has been identified, how do we go about it?

Deconstruct a Tree
------------------

Starting from the FancyTree array structure (array of nested objects as listed above) we want an array of objects with one object per leaf which has the following form:

{"key":"6070aed0","title":"Inbox","parent":0},
{"key":"6070aed1","title":"Drafts","parent":0},
{"key":"6070aed2","title":"Sent","parent":0},
{"key":"6070aed3","title":"Trash","parent":0},
{"key":"6ae8a010","title":"Current","parent":0},
{"key":"6ae8a011","title":"Current-Child-One","parent":"6ae8a010"},{"key":"6ae8a012","title":"Current-Child-Two","parent":"6ae8a010"},{"key":"6ae8a013-6cb8","title":"Archives","parent":0},{"key":"6ae8a014","title":"Archives-Child-One","parent":"6ae8a013"},{"key":"6ae8a015","title":"Archives-Child-Two","parent":"6ae8a013"}] 

All the actions we need to perform on the *FancyTree* (create, rename, move and delete) are performed on this intermediate array.

To obtain this flat deconstructed array we use the following function:

 

     deconstructTree = (tree) ->
        out = []    
        createIntermediateTree = (tree)->    
          for item in tree
            if item?.title 
              item.parent ?= 0   
              intermediateObject = 
                     key : item?.key
                     title : item?.title
                     parent : item?.parent
              out.push intermediateObject                   
              if item?.children 
                 for elem in item.children
                     $.extend(elem, {parent: item.key})
                 createIntermediateTree(item.children) 
        createIntermediateTree(tree)
        out

What is going on in this function?
We call the function *deconstructTree* with the FancyTree JSON object *tree*.
Inside this function we define another function *createIntermediateTree* which we invoke at the end (just before returning *out*).
When *createIntermediateTree* returns we return the array *out*.

*createIntermediateTree* loops over the tree array and for each item it creates/constructs an *intermediateObject* which will be pushed on to the *out* array.
If there are children, we recursively call *createIntermediateTree* for the children.

We end up with a flat deconstructed array.


Reconstruct a Tree
------------------

In order to reconstruct a tree, we use the following function:
 

    reconstructTree = (tree) ->
      createReconstructTree = (tree, parent) ->
        out = []
        for elem in tree
          if elem.parent is parent
             children = createReconstructTree(tree, elem.key)
             if children.length
                elem.folder = true
                elem.children = children
             out.push elem       
        return out
     createReconstructTree(tree, parent= 0)

The function *reconstructTree* takes the previously created flat deconstructed array as an argument and defines the recursive function *createReconstructTree* which is invoked at the end of the first outer function.

*createReconstructTree* takes two arguments: the previously created flat deconstructed array and the root parent key. By definition, the root parent id is set to zero.
*createReconstructTree* is a recursive function, which invokes itself  when the following condition is met:

 

    if elem.parent is parent
      children = createReconstructTree(tree, elem.key)

You need to have in mind the form of the deconstructed flat array; here's an excerpt:

{"key":"6ae8a010","title":"Current","parent":0},
{"key":"6ae8a011","title":"Current-Child-One","parent":"6ae8a010"},
{"key":"6ae8a012","title":"Current-Child-Two","parent":"6ae8a010"},

In this example, when *createReconstruct* is invoked with parent = 0, it will call itself again with *elem.key* *6ae8a010*  **as parent** and then find two children.
For these two children, we create on the *elem*, the property *folder* and *children*, which will be pushed on to the *out* array. 
        

     elem.folder = true
     elem.children = children

This is the structure imposed by *FancyTree*, if there are children, we need to set *folder* to *true*. In our flat deconstructed array, these two properties didn't exist : this is the way you create them whith the so called *dot notation.*

By the way, don't hesitate to put a 

    console.log JSON.stringify out 

 
after the push operation if you want to see progressively what's going on...


Renaming a leaf node
--------------------

So how do you rename a leaf node, once you have deconstructed the base array?

 

     renameNode = (key, newName, tree) ->
        out = []
        for elem in tree
          if elem.key is key
            elem.title = newName
          out.push elem
        out

Well, it couldn't get any simpler! And for the other actions (create, move, delete) it's the about the same, so we'ere not repeating ourselves here.

The next question is : how do you glue these things together? The answer is with functional programming

Gluing things together
----------------------

The three step process described earlier on is realized using functional programming. We'll be using the following libraries

 1. Q Promises 
 2. Ramda



But with much ado, here's one example for renaming a leaf node:
 

     launchRenameNode = (key, newName) ->
        curRenameNode = R.curry(renameNode)
        localRenameNode = curRenameNode(key, newName)
        fetchTree()
           .then deconstructTree
           .then localRenameNode
           .then reconstructTree
           .then (data) ->
              $("#treeThree").fancytree({source: data})  
           .done()

We recognize in the middle of this code our three step process : 

 1. deconstructTree
 2. localRenameCode
 3. reconstructTree

But what does *fetchTree* do exactly?

      fetchTree =  -> Q.when(test.tree) 

The funcion *fetchTree*  returns a Promise with *test.tree* data. 

Now everything is in place to walk through the function *launchRenameNode*.

*launchRenameNode* is invoked with two arguments : the variable *key* of the node we want to rename, and the variable *newName* with the new name of the node. Next we **curry** the function *renameNode*. 

    Currying a function results in a new function that can be partially applied. 
    
    Partially applying a function means to invoke the curried function with only part of the arguments of the original function. 

    This in turn results in a new function that waits for the remaining arguments to be provided later on! 

In our case, you have to keep in mind that the function *renameNode* takes three arguments : *key, newName* and *tree*

The function *curRenameNode* has been curried and is invoked with with two arguments : *key* and *newName*. Initially, *renameNode* takes three arguments : we say that *curRenameNode* has been partially applied!

The function *localRenameNode* still waits for the third argument : *tree*. This third argument will be given to *localrenameNode* by the *fetchTree* function once the promise is resolved, which is immediately in our case.

**It is the joint usage of Q Promises and currying that permits us to glue our functions together the way we did!**

Credits clearly go to Scott Sauyet, you may want to consult his article [Favoring curry!](http://fr.umio.us/favoring-curry/)
