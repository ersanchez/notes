# JavaScript Notes

This is a listing of my JavaScript notes.

## `watchandcode.com` Notes

A list (array) of items is created with brackets around items separated by commas:

	['dog','cat','bird','hippo']

Save your list (array) by assigning its contents to a variable (in this case it will be "animals".

Note: if the items in the list are strings (text), each item must be surrounded by single quotes.

	var animals = ['dog','cat','bird','hippo']

You can display the items in your list by using `console.log()` like this:

	console.log(animals)

You can add (append) an item onto the end of a list by using `push`. So let's add an `elephant` to our `animals` list:

	animals.push('elephant')

Elements of the array are counted left-to-right starting with zero. So, in the animals list `dog` is the 0th item and `bird` is item 2.

You can access a specifit list item by using the variable name for the list followed by the number of the specific item in brackets.

For example, display item 3 from the list:

	animals[3]

...will give you: `"hippo"`

You can change an item in the list by calling the variable name followed by the item number in brackets. Then, set that equal to the new value.

Say you want to change `cat` to `mouse`. 

	animals[1] = 'mouse'

Deleting an item from a list is easy. Just use `splice`. Where...

	variableName.splice(1st item to delete, number of items to delete)

For example, say you want to delete `bird` and `hippo` from the list.

	animals.splice(2,2)
