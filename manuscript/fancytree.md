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


Achieving theses tasks is a three step process:

 1. deconstruct tree
 2. perform action
 3. reconstruct tree







Like this:

	function createJSON() {
    jsonObj = [];
    $("input[class=email]").each(function() {

        var id = $(this).attr("title");
        var email = $(this).val();

        item = {}
        item ["title"] = id;
        item ["email"] = email;

        jsonObj.push(item);
    });

    console.log(jsonObj);
}
Explanation

You are looking for an array of objects. So, you create a blank array. Create an object for each input by using 'title' and 'email' as keys. Then you add each of the objects to the array.

If you need a string, then do

jsonString = JSON.stringify(jsonObj);





> Written with [StackEdit](https://stackedit.io/).