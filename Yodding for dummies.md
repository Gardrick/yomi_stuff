Is this a yodding tutorial? **No.**
Just some coding (and not only) notes I'm going to make that I find useful.
Some are ripped from (now gone) coding guide for dummies, some will just point to other guides (because I'm not going to waste time rewriting what already exists).
## If you already have experience in yodding, get out. Nothing interesting here.

Firstly, some naming explanation (in case person reading this is :clueless:):
- `node` - everything in Scene tab (top left corner of editor)
- `properties`/`settings`/`export variables` - when you click on node, godot opens Inspector tab on the right side, listing node properties
- `fighter`/`top node`/`root node`/`character` - the topmost node in your character's Scene tab, which holds `extends Fighter` script
- `state` - node under `StateMachine` node, which holds some kind of `State` script (`ObjectState`/`CharacterState`/`ThrowState`)

# Chapter 1. Code basics (godot)
In my opinion *gdscript* (coding language of godot) is closest to Python, so if you know anything about Python you should understand almost everything you need to understand in gdscript. But in general if you know **any** coding language this chapter is pretty much useless for you.
small tip: code blocks in Discord are made by writing "\`\`\`" before and after code itself, and highlight (colors) are added by writing language name on first line (right after first "\`\`\`"), although there's no `gdscript` highlight so I'm going to use `swift` instead

## Spaces/tabs
<insert *Unexpected indentation* / *Mixed tabs and spaces* joke here>
yeah I've seen enough of those to actually include this.
Main issue here is actually Discord, replacing all tabulations with spaces when copying code blocks.
So, this ` ` is a space. And this `  ` is a tab. (If you didn't know, there's a *button* on your keyboard for it.) In gdscript those are **necessary** for indentation. And indentation is **necessary** for code to run correctly.
```swift
func _ready():
	print("Hello") # indented once
	if true:
		print("Inside if") # indented twice
```
**Indentations** indicate when part of code *belongs* to some block.
And you **cannot** mix both spaces and tabs in same script for indentation.
tip: `#` makes everything written after it in that line of code a *comment*. Comments are just text, they won't run code, won't do anything. Just plain text.
you can also press `ctrl+k` to comment current *line* (useful when you need to debug some part of code and don't want to delete it temporary), or *lines* if you selected several
When writing code blocks for gdscript in Discord, keep in mind that for some reason Discord copies tab as **2** spaces, when it's **4** spaces in godot. So just double-tab and you'll be fine.
Good news for you is that after pasting code from Discord you can simply `ctrl+s` (to save everything), and godot should automatically convert those spaces back to tabs (if they were correctly double-tabbed).

## Variables and Functions
Lucky for you, variables in godot are *dynamically typed*, so all you have to do is
```swift
var name = value
```
`var` is a keyword to *declare* a variable with `name` and `value` of your choice.
You can even shorten it to `var name` if you don't need value right now - it'll assign `null` value by default, but I wouldn't recommend that.
Variable name's only limit is to **not** start with a number.
Variable value can be any type:
- *int* - just an integer number (`1`, `-169`, `1024`, etc.)
- *bool* - `true`/`false`, pretty self-explanatory I hope
- *float* - number with floating point, I will write a note about this one, please don't skip it, it's important (`1.0`, `-16.234`, `0.9999`, etc.)
- *string* - text (`"a"`, `"abc"`, `"what am I wasting my life on..."`, `"almost any symbols can be here! @256[:->]"`)
- *array* - list of elements, can store any variable types, even arrays again (`[]`, `[0, 1, 2]`, `["abc", 0.1, -5]`, `["a", ["b", "c"], "d"]`)
- *dictionary* - list of pairs `key:value` (`{0: "0", 1: "1", 2: "2"}`, `{"onetwo": [1, 2], 3: 3.0}`)
- *Vector2* - basically a dictionary with `x` and `y` keys: `{"x": 0, "y": 0}`, declared with `Vector2(x, y)` (where `x` and `y` are values you put in)

Now quick note about floats:
It is **not** ideal to use floats for operations that interact with gameplay. I could write some kind of long explanation here, but just know that yomi has an entire FixedMath library **for a reason.** But I'll get back to it later. For now just remember that whenever I mention floats, I will be speaking about *strings* containing those floats (for example `"51.64928"`).
for curious, here's a [link](<https://0.30000000000000004.com/>) about floating point error (which is the reason we do that whole FixedMath thing)

There are also a few special keywords when creating variables:
- `export` - makes variable appear in inspector window when clicking on node (will appear at the bottom of properties)
(`export var my_int = 0`)
NOTE: not necessary, but It's a good habit to define variable *type* when making export: `export (Type) var something = default_value)`
NOTE2: you can also create *categories*, by adding `export var _c_CategoryName = 0`
- `onready` - prevents variable initialization until everything is loaded in, best use is for creating reference variables for nodes in your scene
(`onready var my_node = $"my_node"`)

Another thing that wasn't big enough for own part so I'll put it there: `get_node()`.
This is a very useful function, that allows you to access basically anything in game (but pls don't do that).
`get_node("nodeName")` searches through child nodes of current node (one you wrote that in) and if finds match, returns that node.
`get_node("%nodeName")` searches in your *scene* for node with `scene unique name` (checkbox when you right-click on node) and that name.
You can also shorten it with `$`:
`$NodeName` or `$"%NodeName`
"NodeName" can also have path:
- `/` to get *inside* node
- `..` to get *outside* node
`get_node("StateMachine/Start")` - when called in fighter, would find `Start` node (state)
`get_node("../../")` - when called in state, would find Fighter node
This one actually gets kinda complicated when you start digging into it, but I'll list most used cases later.

Ok back to the theme.
Once you've *declared* variable, you can access it anywhere just by typing it's name. *However*, if you declared it in specific *block*, you won't be able to access it outside:
```swift
var example_1 = 0

func _ready():
	print(example_1) # will print example_1 because it is declared in whole script
	var example_2 = 1

func _exit():
	print(example_2) # will give you error because example_2 is declared only inside _ready()
```
Also you can't use variable that was declared *later* than you're trying to use it, but hopefully that was obvious.

Functions are pretty simple too.
```swift
func name():
	print("Doing something")
```
Keyword `func` declares function, then `name` of your choice, `()` for parameters, and after `:` comes function's *body* - actual code that runs when you call that function.
You call function by writing `name()`, and filling parameters if it has any.
```swift
func add(a, b):
    return a + b

var x = add(1, 1) # will write 2 to x
```
You might've noticed that `return` thing. When you write `return (whatever)` function will *end*, and pass `(whatever)` outside. So in this example I'm basically saying `var x = 1 + 1`.
Parameters (variables) you write in function's parenthesis when *declaring* are variables that will be available inside that function. Usually they *must* be filled when calling function, but you can do `b=0` instead of `b`. When you add `=value` after variable name it gives it *default value*. So when it's called without this variable it will simply set it to default value you've set.
```swift
func add(a, b=0):
    return a + b

var x = add(1) # will write 1 to x because a(1)+b(0)=1
```

## Operators and Conditionals
**Very** useful things. Must know.
- `==` - equal
- `!=` - **not** equal
- `>` - greater than
- `>=` - greater or equal
- `<` - less than
- `<=` - less or equal
- `and` - both must be true
- `or` - at least one must be true
- `not` - inversion (false->true, true->false), can be replaced with `!`
`false`, `null`, `0`, `0.0`, `""` (empty string), `[]` (empty array) -> **false**.
Everything else -> **true**.

`if`/`elif`/`else`:
```swift
if condition1:
	something when condition1 is true
elif condition2:
	something when everything previous is false but condition2 is true
else:
	something when everything previous is false
```
Conditions can be combined in various ways, here are some examples:
```swift
if hp <= 0:
	print("dead lol")
elif hp <= 500:
	print("still alive")
elif hp <= 1000:
	print("fresh and ready")
else:
	print("chonky")
```
```swift
if DOT > 0:
	opponent.take_damage(1)
	DOT -= 1
	$"%particle".emitting = true
else:
	$"%particle".emitting = false
```
```swift
if not style.get("character_color") == null:
	color0 = style.get("character_color")
```
```swift
if current_tick > 36 and host.opponent.get_pos().y == 0 and host.cycle >= 2:
	host.change_state("ob_throw_ex", host.get_facing_int())
```

`match`:
```swift
match expression:
	pattern1:
		do smth
	pattern2:
		do smth else
```
Could be replaced with bunch of `if`s, but looks cleaner that way.
```swift
var number = 0
match number:
	0:
		print("zero")
	1:
		print("one")
	2:
		print("two")
```

## Loops
`for` - loops through each element in `range`, `array` or `dictionary`
```swift
for i in range(5):
    print(i) # prints 0,1,2,3,4 (range goes from 0 to not including value)

for i in range(5, 10):
	print(i) # prints 5,6,7,8,9 (can set start too)

for i in range(0, 10, 2):
	print(i) # prints 0,2,4,6,8 (third parameter is step - how many values are skipped per iteration)
for i in range(10, 0, -2):
	print(i) # prints 10,8,6,4,2 (can also go backwards)
```
```swift
var things_array = ["player", "shuriken", "opponent"]
for thing in things_array:
    print(thing) # "player", "shuriken", "opponent"
```
```swift
var player_stats = {"hp": 100, "mana": 50, "level": 3}
for key in player_stats:
	print(key) # hp mana level
for key, value in player_stats:
	print(key + "=" + value) # hp=100 mana=50 level=3
```

`while` - loops until condition changes
```swift
var timer = 5
while timer > 0:
    print(timer)
    timer -= 1
# 5,4,3,2,1
```

`continue`/`break` - loop controls
`break` – exits the loop immediately (skips the rest of the current iteration and stops).
`continue` – skips the rest of the _current_ iteration and jumps to the next one.
```swift
for i in range(10):
    if i == 3:
        continue # skip i=3
    if i == 7:
        break # stop the loop at i=7
    print(i) # prints 0,1,2,4,5,6
```
```swift
var count = 0
while true: # infinite loop
    count += 1
    if count % 2 == 0:
        continue # skip even numbers
    print(count)
    if count >= 9:
        break # stop at 9
# prints: 1,3,5,7,9
```

And with that we should be done with **the** basics. Don't need to know godot specific built-in functions/variables since yomi kinda uses it's own.

From now on, I'll be mostly referring to [hustledocs](<https://hustledocs.trimaydev.com/#/>), and I **really** recommend to at least try to read through it once. In following chapters I'll mostly skip explanations, because most of stuff is conveniently named, and you can (and *should*) find it in hustledocs if you want more details.
# Chapter 1.5. FixedMath
Yes, I think this one deserves a small chapter for itself.
In code you'll have access to `fixed` variable, which is a reference to FixedMath library.
From it you'll be able to access function you *should* be using to work with floats (remember, the *floats* in *strings*, not actual floats). For example, **velocity** in yomi is stored in floats, but **positions** are integers.
There's a [page in hustledocs](<https://hustledocs.trimaydev.com/#/class_ref/FixedMath>) dedicated to it. Whenever you see `fixed` value, that means it's a FixedMath value, the float in string. You'll find all functions explained if you scroll down a bit, I'll just show you real quick how to use it:
```swift
var x = fixed.add("1.5", "1.5") # "3.0"
var y = fixed.div(str(13), "2.0") # "6.5"
```
tip: since many variables in yomi are integers, and whenever mixing them with floats, you'll have to convert them into strings to use FixedMath, using `str()` function. You can also give it variables:
```swift
var off = Vector2(opos.x-pos.x, opos.y-pos.y)
var forc = fixed.normalized_vec_times(str(off.x), str(off.y), "5")
```
And last note: most yomi functions have a check for variables you give them - they have to be either **both integers** or **both strings**. When it's wrong it'll give you `Assertion failed` error.

# Chapter ? Few editor tips
Wanted to point out some things before going into actual code.
`ctrl+s` - saves everything. Press this **regularly!** (as bonus fixes tabs, as I mentioned earlier)
By pressing script icon next to node in Scene tab you open that node's script.
To add new nodes, you need to right-click existing node and select `Add child node`. This existing node will usually be `StateMachine` when adding states (moves) or state when adding hitboxes. 
- To add new state, right-click `StateMachine`, add child node, add `Node2D`, rename, right-click it, attach script, write `extends CharacterState` inside (replace if anything inside)
- To add hitbox to state, right-click your state, add child node, add `Hitbox`
**When extending/replacing scripts, don't forget to copy (and paste after) node properties, buttons are in top right cornet of inspector window.**
You can also turn off template (option when in extending menu, set it to `Empty`) because it doesn't give you anything useful and you'll have to remove it anyways.
When you see something like:
```swift
func function(something):
	.function(something)
```
, that's called a *supercall*. It's needed to let function from extended script to do what *it* wants to. For example, there's a whole bunch of code in BaseChar.gd `tick` function, and if we extend it and want to add something to `tick`, we have to supercall it, otherwise BaseChar.gd won't be able to run it's `tick`.
Also you should keep in mind that godot's coordinates are a bit different, and by that I mean:
- origin point is at the top-left corner
- `y` axis is flipped (negative is up, positive is down)

Following chapters will contain some info on: most used functions/variables, useful pieces of code, ways to implement things, etc. They will be split in classes.
# Chapter 2. Useful stuff // Fighter
First thing you should do whenever creating a character, is make custom fighter script by extending existing one. (surely you didn't forget tip in previous chapter about extending scripts, right? :clueless:)
So you should end up with `extends Fighter`.
*Important note:* when adding custom variables in fighter, you should include them in `extra state variables` property - this makes them copy over to prediction ghosts (just write them in there, each on new line).

- `obj_name` - unique object name, will be important when saving projectiles references; (same as just `name`?)
- `hp` - current health
- `MAX_BURST_METER`/`MAX_SUPER_METER`/`MAX_SUPERS` - should be pretty straight-forward, those are *constants*, so you can't change them
- `burst_meter`/`bursts_available` - current burst amount
- `super_meter`/`supers_available` - current super amount
quick note about how meters in yomi work:
for example: when `super_meter` reaches `MAX_SUPER_METER`, it resets to `0` and adds `1` to 
- `supers_available`; same works with burst, except you can't have more than 1 `bursts_available`
- `combo_count` - counts **moves**, so move that hits 3 times will just add 1
- `visible_combo_count` - counts **hits**, same number that you see in game UI
- `is_ghost` - true if in prediction
- `sprite` - reference to `Sprite` node
- `opponent` - reference to opponent's fighter
- `objs_map` - contains every object in game that is not disabled (floor/walls/particles are not objects, so actually just fighters and projectiles); 
- `current_di` - ...yeah, the blue wheel (`{"x": int, "y": int}`)
- `current_tick` - counts every frame since character is spawned; however, not recommended to use it; there is a better alternative:
- `game_tick` - unlike `current_tick`, doesn't keep counting during hitlag
- `hitlag_ticks` - ...the uhh freeze thing; can use this to freeze stuff manually
- `turn_frames` - counts every frame since locked in(?); useful if you need something like turn-based cooldown (instead of frame-based)
- `ceiling height` - ceiling position (except it's positive number, so don't forget to `*-1` when comparing to object position)
- `stage_width` - walls position (left one is at `-stage_width`, right one is at `stage_width`)
- `infinite_resources` - true if infinite resources are enabled

- `get_facing_int()` - returns facing direction: -1=left, 1=right
- `set_facing(int)` - sets facing direction
- `get_opponent_dir()` - returns opponent direction: -1=opponent on the left, 1=opponent on the right
- `apply_force("x", "y")` - applies force to character
- `apply_force_relative("x", "y")` - same but "x" is multiplied by facing
- `move_directly(x, y)` - changes *position* by adding to current, speed is untouched
- `move_directly_relative(x, y)` - same but "x" is multiplied by facing
- `get_pos()` - returns current position (`{"x": int, "y": int}`)
btw in yomi origin point is at the center of floor, and all objects positions are relative to that
- `get_vel()` - returns current speed (`{"x": fixed, "y": fixed}`)
- `set_pos(x, y)` - set position directly
- `set_vel("x", "y")` - set speed directly
- `set_grounded(bool)` - sets if object is on the ground / in the air; not so useful on fighters, but will be *very* useful when we'll get to projectiles

- `take_damage(amount:int)` - deal damage (do **not** use `hp -= amount` over this)
```
start_throw_invulnerability()
end_throw_invulnerability()
start_projectile_invulnerability()
end_projectile_invulnerability()
start_aerial_attack_invulnerability()
end_aerial_attack_invulnerability()
start_grounded_attack_invulnerability()
end_grounded_attack_invulnerability()
start_invulnerability()
end_invulnerability()
```
Yeah, I'm not explaining all of those. Read the names.

- `gain_burst_meter(amount:int)` - add burst meter
- `use_burst()` - use full bar of burst
- `use_burst_meter(amount:int)` - use some of burst
- `use_super_bar()` - use 1 super level
- `use_super_meter(amount:int)` - use some of super
- `gain_super_meter(amount:int, stale_amount:fixed)` - does some math to reduce gain depending on combo
- `gain_super_meter_raw(amount:int)` - not affected by stale

*Important note:* when you need to give function a *scene* as a parameter, you need to use 
- `load("path")` or `preload("path")`; `preload` is preferred.
to get path, simply right-click on file in godot's FileSystem and `Copy Path`
- `spawn_particle_effect(scene, position:Vector2, direction:Vector2)` - spawns particle at global coordinates
- `spawn_particle_effect_relative(scene, position:Vector2, direction:Vector2)` - spawns particle at object local coordinates
- `spawn_object(scene, position_x:int, position_y:int, relative:bool, data:array, local:bool)` - spawns object:
	- `relative` - multiplies `position_x` by object facing if true
	- `data` - passes things to object's `Default` state after spawn
	- `local` - to spawn in local object coordinates (true) or in global coordinates (false)
However, last 3 are set to `true, [], true` by default, so you can get away with just:
`spawn_object(scene, position_x:int, position_y:int)`

- `grab_camera_focus()` - does what it says (NOTE: release is not called automatically, so don't forget to release it yourself)
- `release_camera_focus()` - does what it says
- `screen_bump(direction:Vector2, strength:float, duration:float)` - makes screen shake with set parameters
- `global_hitlag(amount:int, force:bool)` - freeze every object
- `current_state()` - get reference of current state object is in
- `change_state("name", data:array)` - changes current state of object, `data` is optional
- `create_speed_after_image(color:Color, lifetime:float)` - spawns afterimage with selected color and duration (in seconds)
- `create_speed_after_image_from_style(which:int)` - spawns afterimage using color from `style_extra_color_1` if `which` = `1`, else `style_extra_color_2`

```swift
func init(pos=null):
	.init(pos)
	# this one is called when player appears in game
```
```swift
func tick():
	.tick()
	# THE GOAT
	# runs every frame
```
```swift
func hit_by(hitbox, force_hit = false):
	.hit_by(hitbox, force_hit)
	# called when you are hit by hitbox
```
```swift
func _on_hit_something(obj, hitbox):
	._on_hit_something(obj, hitbox)
	# called when YOU hit something (obj) with hitbox
```
```swift
func on_got_blocked_by(who):
	# called when you get blocked (by who)
```

# Chapter 3. Useful stuff // States
## Chapter 3.1. ActionUIData (AUID)
Originally I planned to write full explanation myself but luckily I remembered this exists: https://discord.com/channels/1042370928195162132/1405232262072766688
Also this one is useful if you want to change your UI visuals (main thing is to enable `editable children` on UI node and then find textures/colors to change) https://discord.com/channels/1042370928195162132/1206951580159582258

## Chapter 3.2. Actual CharacterState
Main thing you **need** to remember is the fact that every *state* has a `host` variable, that refers to object. Which means you absolutely can (and must) use it in combination with those fighter functions I've mentioned before (ex: `host.apply_force_relative("5", "0"), host.custom_func(param)`). As well as accessing fighter variables (ex: `if host.combo_count > 0:...`, `host.my_var += 1`)
Now a bit about *where* to do that:
```swift
func _tick():
	# runs every tick, just like fighter's tick() but for states
	# doesn't need supercall unlike tick()
```
```swift
func _frame_0():
	# frame functions are called when current_tick matches their number, and you can put any number instead of 0 (as long as it's in your anim length range obviously)
```
```swift
func _on_hit_something(obj, hitbox):
	._on_hit_something(obj, hitbox)
	# works exactly like fighter one
```
NOTE: there's `_enter()` function, but it **will** be called when you're still doing that move and launch prediction, so it's recommended to use `_frame_0()` instead.

- `current_tick` - reminder that it starts counting **from 0**
fun fact: you can actually manipulate this variable, and sometimes it's pretty useful
- `hit_fighter` - very useful variable in case you need to check if your move hit opponent
- `anim_name` - you *can* change it mid-state, but usually it's changed on `_frame_0` (or not changed at all)
- `anim_length` - technically changeable, but shouldn't need to
- `iasa_at`/`iasa_on_hit` - those are a bit more popular in terms of modifying through code

```swift
func is_usable():
	return .is_usable()
	# this is how you make moves appear/disappear when YOU want
	# .is_usable() checks default state accessibility (interrupt strings, stances, grounded/aerial, etc.)
```
```swift
func is_usable():
	return .is_usable() and host.can_use_thing = true
	# to add something, simply add your conditions to the return
	# removing .is_usable() is NOT recommended
```
usually it doesn't get too bad, but it *could* get a bit complicated (don't be scared, you shouldn't ever get in situation where you need all of that)
```swift
func is_usable():
	var finisher_check = ((host.deathmark >= host.opponent.hp or host.deathmark >= 250) and !host.apathy_active) if finisher else true
	var s_check = (host.sword or host.sword_hc) if req_sword else true
	var k1_check = ((host.knife1 or host.knife1_hc) or (host.knife2 or host.knife2_hc)) if req_knife else true
	var k2_check =  ((host.knife1 or host.knife1_hc) and (host.knife2 or host.knife2_hc)) if req_2knives else true
	var rep_check = host.replacement_check(replacement_for) if replacement_for else true
	return .is_usable() and finisher_check and s_check and k1_check and k2_check and rep_check
```

Good guide on interrupt strings: https://discord.com/channels/1042370928195162132/1311830878523101204

NOTE: `static force` and `enter static force` in inspector are literally just `host.apply_force_relative` at set ticks, so if you need more movement in state now you know how to.

Accessing hitboxes from states:
Remember how I explained `get_node()` being useful? Here it is.
```swift
extends CharacterState

onready var my_hb = $Hitbox # gets "Hitbox" child node as soon as it can

func _frame_0():
	if host.strong:
		my_hb.damage = 100 # when editing hitbox properties through code, remember that _on_hit_something is called AFTER hit, so you need to make changes at least 1 frame BEFORE hitbox activates (or hits target)
		my_hb.damage_in_combo = 100 # and don't forget to change this one too
	else:
		my_hb.damage = 50
		my_hb.damage_in_combo = 50
```
Just a little example about how you can change hitbox properties from your state.

Common scripts (core scripts) (extending):
If you have some *base* functionality that you want all your states to share but too lazy to copy over and over (and will have to copy over **again** in case you made change in one of them), you can make common script, and then simply extend other from it.
There's an `Inherits` option when adding new script, so it should be pretty straight forward.
One important thing to mention is that `classname`s do **not** work on export, so when extending, you **will** use path to script.
```swift
extends "res://_GardrickRulebreaker/characters/Rulebreaker/scripts/rb_state.gd"
```
(example how it would look instead of usual `extends CharacterState`)

Small but powerful script that helps managing multihit moves: https://discord.com/channels/1042370928195162132/1474625171103940749

Loops:
Sometimes you want a move to repeat some part few times. For example, dive move that should continue until character hits the ground. But we don't want to just *guess* how long it would take and how long animation should be.
Solution: just loop `current_tick` until we land. Here's small example:
```swift
func _tick():
	if current_tick == 11 and host.get_pos().y < 0:
		current_tick = 8
	# in this example, my fall loop is frames 9-12 (so with current_tick make it -1)
	if current_tick in range(8, 12) and host.get_pos().y == 0:
		current_tick = 12
	# and when we hit the ground, immediately switch to frame after fall loop (so in case we're still in loop, it ends immediately)
```

Spawning/saving projectiles:
I'll cover projectiles themselves in next chapter, but this one fits in States.
```swift
func _frame_5():
	var dir = xy_to_dir(data.x, data.y, "18")
	
	var proj = host.spawn_object(preload("path"), 20, -18)
	proj.set_grounded(false)
	proj.sprite.set_material(host.sprite.get_material())
	proj.apply_force(dir.x, dir.y)
	host.proj_ref = proj.obj_name
```
Now let me explain it real quick:
`xy_to_dir(x, y, "multiplier")` is a very useful function when working with xyplots, returns Fixed vector with length of `"multiplier"`.
`spawn_object` returns object after creating it, so we can easily get access to created projectile (not recommended to use *not* in the same tick when it's spawned).
`proj.set_grounded(false)` - projectiles actually don't know if they're in air or ground when they're spawned, and set to `grounded=true` by default. This results in not being able to apply force *down* to them. So to fix that, we simply tell them to not be grounded.
`proj.sprite.set_material(host.sprite.get_material())` - optional, in case you want to make your projectile follow your fighter style.
`proj.apply_force(dir.x, dir.y)` - you should know by now what happens here, but reminder that without `set_grounded(false)` you couldn't apply force down.
`host.proj_ref = proj.obj_name` if you for any reason need to access this projectile later, save it's `obj_name` in fighter. Then somewhere else you can do `obj_from_name(proj_ref)`  to get projectile.
NOTE: `obj_from_name()` is `BaseObj` function, available only in projectiles/fighters
NOTE2: to check if it's still alive, simply try `obj_from_name(proj_ref)` - it returns either projectile or null if it doesn't exist anymore
