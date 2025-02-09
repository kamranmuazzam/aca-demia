-
- **length** of:
	- some num: that num
	- some value: item length of that value) OR 1
	- some dict: length of Object.keys(that dict)
-
- dict from **state**: dict from state ""
	- some value: State.get(that value
- list from **state**: list from state ""
	- some value: map of this list by $(its 1) then it
		- this list: the pairs of State.get(that value
-
- join **of** some list by some value: join that list by that value:
- **join** some list by some value:
  ```javascript
  function joinBy(arr, separator){
      const kit = {res: [], separator}
      valueOf(arr).forEach(joinBy.kitPush, kit)
      return kit.res
  }
  joinBy.setStatic(function kitPush(item, index){
      if (index > 0) this.res.push(this.separator)
      this.res.push(item)
  })
  const valueOf = Syn.Type.valueOf
  return joinBy
  ```
-
- **append** some value to some list: Array.prototype.push.$call(that list that value
-
- **assign** some value at some word in some dict: Dict.assign(that value, that word, that dict
-
- **iterate** some list: iterate that list by $(give it)
	- by some func: List.forEach(that list, that func
-
- map **of** some list by some item-func: map that list by that item-func
	- then some res-func: map that list by that item-func then that res-func
- **map** some list by some item-func: map that list by that item-func then it
	- then some res-func: List.mapThen(that list, that item-func, that res-func
-
- **pairs** of:
	- some dict: Object.entries(that dict
	- some list: Object.entries(that list
-
- **print** some value: list= List(); depth= -1; on arg append it to that list; give each arg for printing that value; Text(that list as args;
-
- **text** from:
	- some **dict**: text from collection that dict
	- some **list**: text from collection that list
	- **collection** some value: list= List(); give each arg for text of that value; Text(that list as args;
		- on arg: append it to that list
-
- give each **arg** for:
	- **printing**:
		- **collection** some value: depth= ?depth + 1; iterate (the pairs of that value) by given-pair
			- on given-pair: give each arg for printing pair ?given-pair
		- **pair** some pair-value: give-bullet; pair-label; give each arg for printing (?pair's 1)
			- give-bullet: give arg "\n"; give arg (that depth times "\t"); give arg "* "
		- some **block**: give arg (?block's head); give arg "{"; give arg ((?block's body) its id); give arg "}"
		- some **dict**: give each arg for printing collection that dict
		- some **list**: give each arg for printing collection that list
		- some **value**: give arg that value
	- **text** of:
		- **pair** some pair-value: pair-label; give each arg for text of (?pair's 1)
		- some **dict**: give arg "{"; give-pairs; give arg "}"
			- give-pairs: kit= Dict(comma no); iterate (the pairs of that dict) by given-pair
				- on given-pair: check-comma; give each arg for text of pair ?given-pair
					- check-comma: if (?kit's comma) then (give arg ", ") else activate-comma
						- activate-comma: assign yes at comma in ?kit
		- some **list**: give arg "["; give-items; give arg "]"
			- give-items: iterate (join that list by ", ") by $(give each arg for text of it)
		- some **value**: give arg that value
	- pair-label: give arg (?pair's 0); give arg ": "
-
- **random** item of some list: (random num) times (that list's length); item Math.floor(it) of that list
- some list its some word **item**: the that word item of that list
- some word **item** of some list: the that word item of that list
- **the**:
	- some word **item** of some list: the that word of that list
	- **first**: rather the first of it
		- of some list: item 0 of that list
	- **second**: rather the second of it
		- of some list: item 1 of that list
	- **third**: rather the third of it
		- of some list: item 2 of that list
	- **penultimate** of some list: item -2 of that list
	- **last** of some list: item -1 of that list
-
- **its** some key-value: rather it its that key
- some obj-value its: rather that obj its it
	- some key-value: that key of that obj
-
- **of** some obj-value: rather it of that obj
- some key-value of: rather that key of it
	- some obj-value: item that key of that obj
-
- **item**:
	- some num **of** some list: List.elementOf(that num that list
	- some key-value: rather item that key of it
		- **of** some value: Dict.itemOf(that key that value
	- **of**:
		- some list **at** some num: List.elementOf(that num that list
		- some value **at** some key-value: Dict.itemOf(that key that value
-
- **member** some text of some value: item of that value at that text
-
- **keys** of some value: if Evaluation.is(that value) then Object.keys(that value) else []
- **values** of some value: if Evaluation.is(that value) then Object.values(that value) else []
-
- $execute
  ```javascript
  logseq.Module.Array = Array
  logseq.Module.Object = Object
  const getFunc = Syn.Expression.Holder.getFunc
  const Is = Syn.Evaluation.is
  const lookup = Syn.Normal.prototype.evalIn.forType.lookup
  const Void = Syn.Word("void")
  
  Syn.Dict = function Dict(...args){
      const obj = {}
      for (let i = 0; i < args.length; i += 2) {
          obj[args[i]] = args[i + 1] ?? null
      }
      return obj
  }
  .setStatic(function assign(val, key, dict){
      if (key !== "__proto__" && dict.constructor === Object) dict[key] = val
  })
  .setStatic(function itemOf(key, obj){
    const val = Object.getOwn(obj, key)
    return (val === null || val === undefined) ? Void : val
  })
  
  const List = Syn.List = function List(...args){ return args }
  .setStatic(function elementOf(index, list){
    const val = (index < 0) ? list[list.length + index] : list[index]
    return (val === null || val === undefined) ? Void : val
  })
  .setStatic(function forEach(args, onItemGiven){
      const func = this.getFunc(onItemGiven || this.getState("item-given"))
      const res = (!Function.hasInst(func)) ?
                  Syn.error.call(this, "non-func item-given")
                : (!Array.isArray(args)) ?
                  this.func.call(null, this.context.$it = args)
                : List.kitNext.call({args, context: this, func, i: 0})
      return (Promise.hasInst(res)) ? res.then(List.retvoid) : Void
  })
  .setStatic(function kitNext(val){
      if (this.items && val !== undefined) {
          if (Syn.Typed.isNorm(val) && val.meta === ".args") {
              const body = val.body
              if (Array.isArray(body)) this.items.push(...body)
              else this.items.push(body)
          } else {
              this.items.push(val)
          }
      }
      if (this.i >= this.args.length) {
          return (!this.items) ? Void
               : this.last.call(null, this.context.$it = this.items)
      }
  
      const res = this.func.call(null, this.context.$it = this.args[this.i++])
      return (!Promise.hasInst(res)) ? List.kitNext.call(this, res)
           : res.then(List.kitNext.bind(this))
  })
  .setStatic(function mapThen(args, onItemGiven, onResGiven){
      const func = this.getFunc(onItemGiven || this.getState("item-given"))
      const last = this.getFunc(onResGiven || this.getState("res-given"))
      return (!Function.hasInst(func)) ?
             Syn.error.call(this, "non-func item-given")
           : (!Function.hasInst(last)) ?
             Syn.error.call(this, "non-func res-given")
           : List.kitNext.call({args: (Array.isArray(args) ? args : [args]),
                                context: this, func, i: 0, items: [], last})
  })
  .setStatic(function retvoid(){ return Void })
  ```
- .