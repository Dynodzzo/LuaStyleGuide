# Lua Style Guide
*The guidelines that I apply myself to follow for my Lua projects.*

## Table of contents
1. [Types](#types)
1. [Tables](#tables)
1. [Strings](#strings)
1. [Properties](#properties)
1. [Variables](#variables)
1. [Comparison Operators & Equality](#comparison-operators--equality)
1. [Blocks](#blocks)
1. [Comments](#comments)
1. [Whitespace](#whitespace)
1. [Commas](#commas)
1. [Semicolons](#semicolons)
1. [Type Casting & Coercion](#types-casting--coercion)
1. [Naming conventions](#naming-conventions)

## Types
- **Primitives** - When you access a primitive type, you work directly with its value.

  - `string`
  - `number`
  - `boolean`
  - `nil`

```lua
local foo = 1;
local bar = foo;

bar = 2;

print(foo, bar); -- => 1, 2
```

- **Complex** - When you access a complex type, you work on a reference to its value.

	- `table`
	- `function`
	- `userdata`
	- `thread`

```lua
local foo = {1, 2};
local bar = foo;

bar[1] = 0;

print(foo[1], bar[1]) -- => 0, 0
```

## Tables
- Don't use [reserved words](http://www.lua.org/pil/1.3.html) as keys.

```lua
-- bad
local character = {
	name = 'SpongeBob',
};

character['function'] = 'Cook';

print(character.function); -- 'name' expected near 'function'


-- good
local character = {
	name = 'SpongeBob',
	job  = 'Cook'
};
```

- Use readable synonyms in place of reserved words.

```lua
-- bad
local character = {
	func = 'Cook'
};

-- good
local character = {
	job = 'Cook'
};
```

- Use `table.insert` instead of direct assignment to insert items which are not associated with a key.

```lua
local characters = {};

-- bad
characters[1] = 'SpongeBob';
characters[2] = 'Patrick';

-- good
table.insert(characters, 'SpongeBob');
table.insert(characters, 'Patrick');
```

## Strings

- Use single quotes `''` for strings.

```lua
-- bad
local name = "SpongeBob";

-- good
local name = 'SpongeBob';

-- bad
local fullName = "Spongebob" .. lastName;

-- good
local fullName = 'SpongeBob' .. lastName;
```

- Strings longer than 80 characters should be written across multiple lines using the double square brackets.

```lua
-- bad
local errorMessage = 'This is a very long message that will definitely not fit in an eighty characters long line. And adding text like this will not help at all ! Ok, a last little sentence and I\'ll stop...';

-- bad
local errorMessage = 'This is a very long message that will definitely not ' ..
'fit in an eighty characters long line. And adding text like this will ' ..
'not help at all ! Ok, a last little sentence and I\'ll stop...';

-- good
local errorMessage = [[This is a very long message that will definitely not fit
in an eighty characters long line. And adding text like this will not help at
all ! Ok, a last little sentence and I'll stop...]];
```

- When programmatically building up a string, use `table.concat` and `string.format`.

```lua
local people;
local length;

people = {
	{name = 'SpongeBob', color = 'Yellow'},
	{name = 'Patrick', color = 'Pink'},
	{name = 'Carlo', color = 'Green'}
};

-- bad
function serializePeople(people)
	local items = 'People :\n';
	local index;
	local item;
	
	for index, item in pairs(people) do
		items = items .. index .. '. This is ' .. item.color .. ' ' .. item.name .. '\n';
	end
	
	return items;
end

-- good
function serializePeople(people)
	local items = {};
	local index;
	local item;
	
	for index, item in pairs(people) do
		table.insert(items, string.format('%u. This is %s %s%s', index, item.color, item.name, '\n'));
	end
	
	return 'People :\n' .. table.concat(items);
end
```

## Properties

- Use the dot notation when accessing properties.

```lua
local character = {
	name = 'SpongeBob',
	color = 'Yellow'
};

-- bad
local characterColor = character['color'];

-- good
local characterColor = character.color;
```

- Use the brackets notation when accessing properties with a variable.

```lua
local character = {
	name = 'SpongeBob',
	color = 'Yellow'
};

function getProperty(propertyName)
	return character[propertyName];
end

local characterColor = getProperty('color');
```

## Variables

- Always use `local` to declare variables. Not doing so will result in global variables and we want to avoid polluting the global namespace.

```lua
-- bad
character = 'Bob';

-- good
local character = 'Bob';
```

- Use one `local` declaration per variable. It's easier to add new variable declarations this way, and you never have to worry about swapping out a `;` for a `,` or introducing punctuation-only diffs.

```lua
-- bad
local character = 'Bob',
      color = 'Yellow',
      hasPants = true;

-- good
local character = 'Bob';
local color     = 'Yellow';
local hasPants  = true;
```

- Declare unassigned variables last. This is helpful when later on you might need to assign a variable depending on one of the previous assigned variables.

```lua
-- bad
local character = 'Bob',
      color,
      hasPants = true;

-- bad
local character = 'Bob';
local color;
local hasPants = true;

-- good
local character = 'Bob';
local hasPants = true;
local color;
```

## Comparison Operators & Equality

- Conditional statements such as the `if` statement evaluate their expression using coercion and always follows these simple rules :

  + **tables** evaluate to **true**
  + **nil** evaluates to **false**
  + **booleans** evaluate to **the value of the boolean**
  + **numbers** evaluate to **true**
  + **strings** evaluate to **true**

```lua
if ({}) then
	-- true
	-- Tables evaluate to true
end
```

- Use shortcuts.

```lua
-- bad
if (name ~= nil) then
	-- stuff
end

-- good
if (name) then
	-- stuff
end

-- bad
if (hasPants == true) then
	-- stuff
end

-- good
if (hasPants) then
	-- stuff
end
```

## Blocks

- Always use multiline blocks.

```lua
-- bad
if (test) then return false; end

-- good
if (test) then
	return false;
end
```

- Always use parenthesis to enclose the conditional statement.

```lua
-- bad
if test then
	return false;
end

-- good
if (test) then
	return false;
end
```

## Comments

- Use `--[[ ... --]]` for multi-line comments. Include a description, specify types and values for all parameters and return values.

```lua
-- bad
-- add() Adds two numbers
-- passed as parameters
-- 
-- @param {number} a
-- @param {number} b
--
-- @return {number}
function add(a, b)
	return a + b;
end

-- good
--[[
- add() Adds two numbers
- passed as parameters
- 
- @param {number} a
- @param {number} b
-
- @return {number}
--]]
function add(a, b)
	return a + b;
end
```

- Use `--` for single line comments. Place single line comments on a newline above the subject of the comment. Put an empty line before the comment.

```lua
-- bad
local hasPants = true; -- wear pants at start

-- good
-- wear pants at start
local hasPants = true;

-- bad
function makeBurger()
	print('Making burger...');
	-- Attempts to create a burger
	local burger = character.makeBurger();
	
	return burger;
end

-- good
function makeBurger()
	print('Making burger...');
	
	-- Attempts to create a burger
	local burger = character.makeBurger();
	
	return burger;
end
```

- Prefixing your comments with `FIXME` or `TODO` helps other developers quickly understand if you're pointing out a problem that needs to be revisited, or if you're suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable. The actions are `FIXME -- need to figure this out` or `TODO -- need to implement`.

- Use `-- FIXME:` to annotate problems.

```lua
function getMaxBurgers() {
	
	-- FIXME: shouldn't use a global here
	maxBurgers = 0;
	
	return maxBurgers;
}
```

- Use `-- TODO:` to annotate solutions to problems.

```lua
function Calculator() {
	
	-- TODO: total should be configurable by an options param
	self.maxBurgers = 0;
	
	return this;
}
```

## Whitespace

- Use soft tabs set to 4 spaces.

```lua
-- bad
function ()
∙∙local name;
end

-- bad
function ()
∙∙∙local name;
end

-- good
function ()
∙∙∙∙local name;
```

- Place 1 space before the leading brace or the keyword starting a block.

```lua
-- bad
if (test)then
	// stuff
end

-- good
if (test) then
	// stuff
end

-- bad
character.set('infos',{
	name  = 'SpongeBob',
	color = 'Yellow'
});

-- good
character.set('infos', {
	name  = 'SpongeBob',
	color = 'Yellow'
});
```

- Place 1 space before the opening parenthesis in control statements. Place no space before the argument list in function calls and declarations.

```lua
-- bad
if(hasPants) then
	-- stuff
end

-- good
if (hasPants) then
	-- stuff
end

-- bad
function createBurger ()
	-- stuff
end

-- good
function createBurger()
	-- stuff
end
```

- Set off operators with spaces.

```lua
-- bad
local x=y+1;

-- good
local x = y + 1;
```

- End files with a single newline character.

```lua
-- bad
function createBurger()
	-- stuff
end

-- bad
function createBurger()
	-- stuff
end↵
↵

-- good
function createBurger()
	-- stuff
end↵
```

- Leave a blank line after blocks and before the next statement.

```lua
-- bad
if (foo) then
	return bar;
end
return baz;

-- good
if (foo) then
	return bar;
end

return baz;

-- bad
local foo = {
	bar = function()
		-- stuff
	end,
	baz = function()
		-- stuff
	end
};

-- good
local foo = {
	bar = function()
		-- stuff
	end,
	
	baz = function()
		-- stuff
	end
};
```

## Commas

- Trailing commas : **Nope**.

```lua
-- bad
local character = {
	name = 'Bob'
	, color = 'Yellow'
	, hasPants = true
};

-- good
local character = {
	name = 'Bob',
	color = 'Yellow',
	hasPants = true
}
```

- Additional trailing comma : **Nope**.

```lua
-- bad
local character = {
	name = 'Bob',
	color = 'Yellow',
	hasPants = true,
};

-- good
local character = {
	name = 'Bob',
	color = 'Yellow',
	hasPants = true
}
```

## Semicolons

- **Yup**.

```lua
-- bad
function foo()
	local bar = 1
	
	if (bar) then
		return baz
	end
end

-- good
function foo()
	local bar = 1;
	
	if (bar) then
		return baz;
	end
end
```

## Type Casting & Coercion

- Use the `tostring` method to convert into a `string`.

```lua
-- bad
local foo = '' .. bar;

-- good
local foo = tostring(bar);
```

## Naming conventions

- Avoid single letter names. Be descriptive with your naming.

```lua
-- bad
function u()
	-- stuff
end

-- good
function update()
	-- stuff
end
```

- Use lowerCamelCase when naming objects, functions, and instances.

```lua
-- bad
local iTeMsss = {};
local my_object = {};
local o = {};
function c() end

-- good
local myObject = {};
function myFunction() end
```

- Use CascalCase when naming classes.

```lua
-- bad
function user(name)
	self.name = name
end

-- good
function User(name)
	self.name = name;
end
```

- Use a leading underscore `_` when naming private properties.

```lua
-- bad
self.__name = 'Bob';
self.name = 'Bob';

-- good
self._name = 'Bob';
```

- Use capitals separated with an underscore `_` when naming constant properties or variables.

```lua
-- bad
myConstant = 'Bob';
self.max_Age = 'Bob';

-- good
MY_CONSTANT = 'Bob';
self.MAX_AGE = 125;
```