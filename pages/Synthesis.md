-
- Code
	- Synthesis
	  collapsed:: true
		- ```javascript
		  const Module = logseq.Module
		  const LS = Module.Logseq = logseq.api
		  const Kits = Module.Kits
		  
		  Function.prototype.setProto = function setProto(func){
		      this.prototype[func.name] = func
		      return this
		  }
		  Function.setProto(function setStatic(func){
		      this[func.name] = func
		      return this
		  })
		  .setProto(function addHasInst(){
		      this.hasInst = Function.thisHasInst.bind(this)
		      return this
		  })
		  .setProto(function onType(func){
		      (this.forType ||= {})[func.name] = func
		      return this
		  })
		  .setStatic(function thisHasInst(inst){
		      return (inst instanceof this)
		  })
		  .addHasInst()
		  
		  Array.setStatic(function appendOne(one){
		      this.push(one)
		      return this
		  })
		  
		  Object.setStatic(function getOwn(obj, name){
		      return (Object.hasOwn(obj, name)) ? obj[name] : undefined
		  })
		  
		  Promise.addHasInst()
		  
		  const Syn = Module.setChild("Synthesis")
		  .setStatic(function error(msg, content){
		      return Syn.Error(msg, ": " + content + " , in " + this.constructor.name)
		  })
		  .setStatic(function htmlFor(obj){
		      if (obj === null || obj === undefined) return ["void"]
		      if (typeof obj === "string") {
		          if (obj.length > 1024 || obj.indexOf("\n", 1) < 0) {
		              if (!obj.startsWith("\n")) return [obj]
		  
		              const c = obj[1]
		              return [(c === "│" || c === "├" || c === "└") ? obj + " " : obj]
		          } 
		      } else {
		          if (obj.constructor === Object) {
		              if (Object.keys(obj).length < 3) {
		                  return Syn.outFromObj(obj, htmlFor).flat()
		              }
		          } else if (Array.isArray(obj)) {
		              if (obj.length < 3) return Syn.outFromObj(obj, htmlFor).flat()
		          } else if (obj.dict?.constructor === Object) {
		              return htmlFor(obj.dict)
		          } else {
		              return [Syn.stringify(obj)]
		          }
		      }
		  
		      return [buttonFromInfo("...", "dots", Syn.onDotsClicked.bind(null, obj))]
		  })
		  .setStatic(function onDotsClicked(obj, e){
		      const out = (typeof obj === "string") ? [obj]
		                : Syn.outFromObj(obj, Syn.safeHtmlFor).flat()
		      e.target.replaceWith(...out)
		  })
		  .setStatic(function outFromObj(obj, onItemGiven){
		      const isArray = Array.isArray(obj)
		      const arr = [isArray ? "[" : "{"]
		      for (const key in obj) {
		          if (!Object.hasOwn(obj, key)) break
		  
		          const val = obj[key]
		          if (val === undefined) continue
		  
		          if (arr.length > 1) arr.push(", ")
		          if (isArray) arr.push(onItemGiven(val))
		          else arr.push(key, ": ", onItemGiven(val))
		      }
		      arr.push(isArray ? "]" : "}")
		      return arr
		  })
		  .setStatic(function pageBy(by){
		      return LS.get_page(by)
		  })
		  .setStatic(function safeHtmlFor(obj){
		      try {
		          return Syn.htmlFor(obj)
		      } catch(er) {
		          return (er.name !== "RangeError") ? String(obj)
		               : [buttonFromInfo("...∞...", "dots",
		                                 Syn.onDotsClicked.bind(null, obj))]
		      }
		  })
		  .setStatic(function safeStringFor(obj){
		      try {
		          return Syn.stringFor(obj)
		      } catch(er) {
		          return (er.name === "RangeError") ? "[circular]" : String(obj)
		      }
		  })
		  .setStatic(function stringFor(obj){
		      return (typeof obj === "string" ? JSON : Syn).stringify(obj)
		  })
		  .setStatic(function stringForCode(code){
		      const index = code.indexOf("\n")
		      return code.slice(0, (index < 0 || index > 59) ? 59 : index)
		  })
		  .setStatic(function stringify(obj){
		      if (obj === null || obj === undefined) return "void"
		  
		      const contor = obj.constructor
		      return (contor === Object) ? Syn.outFromObj(obj, Syn.stringFor).join("")
		           : (contor === Array) ? "[" + obj.map(Syn.stringFor).join(", ") + "]"
		           : (!Function.hasInst(obj)) ? String(obj)
		           : "<" + Syn.stringForCode(String(obj)) + "...>"
		  })
		  Syn.blocksTreeOfPage = LS.get_page_blocks_tree
		  
		  Syn.constructor.setProto(function toString(){
		      return this._key
		  })
		  
		  function buttonFromInfo(textContent, classname, clickHandler){
		      const button = document.createElement("button")
		      button.classList.add(classname)
		      button.textContent = textContent
		      if (clickHandler) button.addEventListener("click", clickHandler)
		      return button
		  }
		  
		  const Tree = Syn.Tree = function Tree(){}
		  .setStatic(function button(textContent, clickHandler){
		      return buttonFromInfo(textContent, "tree-button", clickHandler)
		  })
		  .setStatic(function node(button, body){
		      const div = document.createElement("div")
		      div.classList.add("tree-node")
		      div.append(button, body)
		      return div
		  })
		  .setStatic(function toggleNode(e){
		      e.preventDefault()
		      e.stopPropagation()
		  
		      const button = e.target
		      const isExpanded = (button.textContent === "-");
		      button.textContent = (isExpanded) ? "+" : "-"
		      button.parentElement.lastChild
		      .style.setProperty("display", (isExpanded ? "none" : "inline-table"))
		  })
		  
		  const cells = document.getElementsByClassName("kit cell")
		  var eid = 0
		  var inEval = 0
		  
		  const SpanEval = Syn.Span = function SpanEval(isRoot, span, type, what){
		      const f = Object.getOwn(SpanEval.prototype.next.forType, type)
		      if (!f) return // invalid
		  
		      const divBlock = span.closest(".ls-block")
		      if (!divBlock) return // invalid
		  
		      if (isRoot) {
		          if (inEval < 1) eid += 1
		          inEval += 1
		      }
		  
		      const block = LS.get_block(divBlock.getAttribute("blockid"))
		      const container = span.closest(".block-main-container")
		      const cells = container.querySelectorAll(".cell")
		      const cellindex = Array.prototype.indexOf.call(cells, span)
		      const divOut = Kits.onParentEvalStarted(container)
		      divOut.classList.add("log")
		      const spanEval = {block, cellindex, divOut, eid, isRoot,
		                        logger: Syn.Evaluation.Logger(Syn.dfltState),
		                        span, type, what, __proto__: SpanEval.prototype}
		      queueMicrotask(spanEval.eval.bind(spanEval))
		  }
		  .setStatic(function onKeydown(e){
		      if (e.key === "Escape") {
		          const span = this.span
		          span.querySelector("input").value = span.syn.init
		      }
		      if (e.key === "Enter") return synthesis(this.span)
		      if (e.code === "KeyZ") return e.stopPropagation()
		  })
		  .setStatic(function setCell(span, eid, val){
		      span.syn.eid = eid
		      span.syn.val = val
		      span.querySelector("b").textContent = Syn.stringify(val)
		  })
		  .setProto(function eval(){
		      const res = Syn.Evaluation.eval(this)
		      if (this.type === "cell") {
		          const span = this.span
		          if (!span.syn) {
		              const init = span.querySelector("b").textContent
		              span.syn = {eid: 0, init, val: null}
		              const handler = SpanEval.onKeydown.bind(this)
		              span.querySelector("input").addEventListener("keydown", handler)
		          }
		          SpanEval.setCell(span, this.eid, res)
		      }
		      const isProm = Promise.hasInst(res)
		      if (!isProm) this.next(res)
		      else res.then(this.next.bind(this))
		  })
		  .setProto(function next(res){
		      const log = next.forType[this.type].call(this, res)
		      const logger = this.logger
		      if (logger.state.$log_target === Syn.Word("console")) logger.log("", log)
		      this.renderOut(logger.print() || [])
		      if (!this.isRoot) return // done
		  
		      if (inEval > 0) inEval -= 1
		      if (inEval < 1) Array.prototype.forEach.call(cells, synthesis.evalSpan)
		  }
		  .onType(function cell(res){
		      const span = this.span
		      const input = span.querySelector("input")
		      span.syn.val = res
		      span.querySelector("b").textContent = Syn.stringify(res)
		      span.querySelector("input").setAttribute("value", span.dataset.what)
		      return span
		  })
		  .onType(function eval(res){
		      const Type = Syn.Type
		      const out = (Type.of(res) === Type.Html) ? Type.valueOf(res)
		                : Kits.createElementOfClass("span", "eval", ...this.strs(res))
		      this.span.replaceWith(out)
		      return out
		  })
		  .onType(function once(res){
		      const content = this.block.content
		      const macroName = this.span.closest(".macro").dataset.macroName
		      const macroStart = content.indexOf("{{" + macroName)
		      const premacro = content.slice(0, macroStart)
		      const postmacro = content.slice(content.indexOf("}}", macroStart) + 2)
		      LS.update_block(this.block.uuid, premacro + Syn.stringFor(res) + postmacro)
		      return res
		  })
		  )
		  .setProto(function renderOut(out){
		      const divOut = this.divOut
		      if (out.length < 8) return divOut.remove()
		  
		      const args = out.map(Syn.safeHtmlFor).flat()
		      const spanOut = Kits.createElementOfClass("span", "out", ...args)
		      divOut.lastChild.replaceWith("=>", spanOut)
		  
		      const expand = (this.logger.state.$log_visibility === Syn.Word("expand"))
		      const span = divOut.lastChild
		      if (!expand) span.style.setProperty("display", "none")
		      const sign = (expand) ? "-" : "+"
		      divOut.append(Tree.node(Tree.button(sign, Tree.toggleNode), span))
		  })
		  .setProto(function strs(res){
		      try {
		          return [Syn.stringify(res)]
		      } catch(er) {
		          return Syn.safeHtmlFor(res)
		      }
		  })
		  
		  const synthesis = logseq.kits.synthesis = function synthesis(span){
		      logseq.kits.synthesis.evalSpan(span)
		  }
		  .setStatic(function evalSpan(span){
		      const isRoot = (this === synthesis)
		      if (!isRoot && span.syn?.eid === eid) return // done
		  
		      const what = span.dataset.what
		      const type = span.dataset.type
		      if (type !== "parse") return SpanEval(isRoot, span, type, what)
		  
		      const parsed = Syn.Expression.fromParsing(Syn.Expression("", what))
		      var str = Syn.stringFor(parsed)
		      if (str.startsWith("eval( ")) str = str.slice(6, -2)
		      span.replaceWith(what, "\n", str)
		  })
		  ```
		- Classes
			- ```javascript
			  const Module = logseq.Module
			  Module.Clipboard = navigator.clipboard
			  Module.Date = Date
			  Module.Promise = Promise
			  Module.self = Module
			  const Syn = Module.Syn = Module.Synthesis
			  
			  const Norm = Syn.Normal = function Norm(type, head = null,
			                                          body = null, meta = null){
			      return {type, head, body, meta, __proto__: Norm.prototype}
			  }
			  .addHasInst()
			  .setStatic(function forTypeOf(type, concept){
			      const norm = Norm.bind(null, type)
			      if (!concept) return norm
			  
			      norm.isNorm = Norm.isType.bind(null, type)
			      return concept[type[0].toUpperCase() + type.slice(1)] = norm
			  })
			  .setStatic(function isType(type, obj){
			      return Norm.hasInst(obj) && obj.type === type
			  })
			  .setProto(function toJSON(){
			      const head = this.head
			      return this.type + (typeof head !== "string" ? "" : "{" + head + "}")
			  })
			  .setProto(function toString(){
			      const f = toString.forType[this.type]
			      return (f) ? f.call(this) : this.type + "{" + (this.head ?? this.body) + "}"
			  }
			  .onType(function error(){
			      return "$ERR{" + this.head + "}"
			  })
			  .onType(function typed(){
			      return (this.meta === ".word") ? this.body
			           : (typeof this.body === "number") ? this.body + " " + this.head
			           : this.head + "{" + Syn.stringFor(this.body) + "}"
			  })
			  )
			  
			  const Error = Norm.forTypeOf("error", Syn)
			  .setStatic(function inListOrCallThis(list){
			      return list.find(Error.isNorm) || this(list)
			  })
			  
			  const Typed = Norm.forTypeOf("typed", Syn)
			  
			  const Word = Syn.Word = function Word(name){
			      const word = Object.getOwn(words, name)
			      return word || (words[name] = Typed("word", name, ".word"))
			  }
			  .setStatic(function isNorm(obj){
			      return Typed.isNorm(obj) && obj.meta === ".word"
			  })
			  const words = Syn.words = {}
			  const No = Word("no")
			  const Yes = Word("yes")
			  Syn.keywords = {
			      false: No,
			      let: Word("let"),
			      no: No,
			      null: Word("void"),
			      on: Word("on"),
			      rather: Word("rather"),
			      true: Yes,
			      void: Word("void"),
			      yes: Yes,
			      "etc.": Word("etc."),
			      do: Word("do"),
			      return: Word("return"),
			      word: Word("word")
			  }
			  Syn.dfltState = {
			      $log: Word("no"),
			      $log_visibility: Word("collapse"),
			      custom: {}
			  }
			  
			  Syn.closing = {
			      '"': '"',
			      "$": "",
			      "'": "'",
			      "/": "",
			      "[": "]",
			      "{": "}"
			  }
			  Syn.pages = {}
			  Syn.types = {}
			  
			  const JS = Syn.JS = function JS(){}
			  JS.console = console
			  JS.isNaN = function _isNaN(obj){ return isNaN(obj) ? Yes : No }
			  JS.new = function _new(_class, ...args){ return new _class(...args) }
			  
			  const Type = Syn.Type = function Type(global, local){
			      return Norm("typed", "." + global, local || global, ".type")
			  }
			  .setStatic(function fromChain(chain){
			      const index = chain.lastIndexOf("-")
			      if (index < 0) return Syn.types[chain] ||= Type(chain)
			  
			      const left = chain.slice(0, index)
			      const right = chain.slice(index + 1)
			      const isnum = !isNaN(Number(right))
			      return (isnum) ? Type(left, chain) : Type(right, left)
			  })
			  .setStatic(function of(arg){
			      if (arg === true || arg === false || arg === null || arg === undefined) {
			          return Type.Word
			      }
			      return (Array.isArray(arg)) ? Type.List :
			             (Typed.isNorm(arg)) ?
			                 (arg.head.startsWith("html") ? Type.Html : Syn.types[arg.head]) :
			             (Error.isNorm(arg)) ? Syn.types.error :
			             Object.prototype.toString.call(arg) === "[object Date]" ? Type.Time :
			             (arg.constructor?.name.startsWith("HTML")) ?
			             Type.Html : Type.byjs[typeof arg]
			  })
			  .setStatic(function valueOf(arg){
			      return (Typed.isNorm(arg)) ? arg.body : arg
			  })
			  
			  const types = ["dict", "error", "func", "html", "list",
			                 "num", "text", "time", "value", "word"]
			  types.forEach( (name)=>{
			      Type[name[0].toUpperCase() + name.slice(1)] = Type.fromChain(name)
			  })
			  
			  Type.byjs = {
			      function: Type.Func,
			      number: Type.Num,
			      object: Type.Dict,
			      string: Type.Text
			  }
			  ```
	- Expression
	  collapsed:: true
		- ```javascript
		  const Module = logseq.Module
		  const Syn = Module.Synthesis
		  const Norm = Syn.Normal
		  const Word = Syn.Word
		  const Expr = Syn.Expression = Norm.forTypeOf("expr", Syn)
		  const That = Word("that")
		  const This = Word("this")
		  
		  Norm.prototype.toString
		  .onType(function cell(){
		      const span = this.meta.DOM.DOM.l?.querySelector(".cell")
		      if (span) {
		          const text = span.querySelector("input").value
		          if (text !== " :cell") return "{ " + text + " }"
		      }
		      return Syn.stringFor(this.body)
		  })
		  .onType(function expr(){
		      return this.head + "{ " + this.body + " }"
		  })
		  .onType(function getter(){
		      return this.meta + "." + this.body
		  })
		  .onType(function holder(){
		      const simple = (this.head === "$" || !Expr.isNorm(this.meta))
		      return (simple) ? "$( " + Syn.stringFor(this.body) + " )"
		           : this.toString.forType.keyed.call(this, "on")
		  })
		  .onType(function keyarg(){
		      return this.toString.forType.keyed.call(this, "let")
		  })
		  .onType(function keyed(key){
		      return "( " + key + " " + this.head.replaceAll("_", "-") +
		             " " + Syn.stringFor(this.body) + " )"
		  })
		  .onType(function lookup(){
		      const head = this.head
		      return (head !== That && head !== This) ? head
		           : "( " + head.body + " " + this.body + " )"
		  })
		  .onType(function train(){
		      const head = this.head
		      const isCall = (typeof head === "string" && head.length &&
		                      head[0] !== head[0].toLowerCase())
		      return (this.head || "") + "( " +
		          this.body.map(Syn.stringFor).join(isCall ? ", " : "; ") + " )"
		  })
		  .onType(function vector(){
		      const head = this.head
		      const isCaller = (head.type === "caller")
		      const args = (isCaller) ? this.body.map(Syn.stringFor)
		                 : head.body.map(vector.kitWord, {body: this.body, i: 0})
		      const separator = (isCaller) ? ", " : " "
		      return (isCaller ? head.body : "") + "( " + args.join(separator) + " )"
		  }
		  .setStatic(function kitWord(word){
		      return (word.startsWith(".")) ? Syn.stringFor(this.body[this.i++]) : word
		  })
		  )
		  
		  const Cell = Norm.forTypeOf("cell", Expr)
		  
		  const BACKTICKS = "`".repeat(3)
		  const Code = Norm.forTypeOf("code", Expr)
		  .setStatic(function exec(code){
		      const head = code.head
		      const str = (head._key !== "javascript") ? code.body :
		                  "const Syn = logseq.Module.Synthesis\n" + code.body
		      if (!head.eval) {
		          const prom = Module.Kits.loadDependencies( {key: head} )
		          head.eval = ()=>prom.then( ()=>head.eval(str) )
		      }
		      return head.eval(str)
		  })
		  .setStatic(function fromText(text){
		      const codeStart = text.indexOf(BACKTICKS) + 3
		      if (codeStart < 3) return // invalid
		  
		      const strLang = text.slice(codeStart, text.search(/\w\W/) + 1)
		      var lang = logseq.Language[strLang]
		      if (!lang) return // invalid
		  
		      const lineEnd = text.indexOf("\n", codeStart) + 1
		      const codeEnd = text.indexOf(BACKTICKS, lineEnd) - 1
		      if (codeEnd > -1) return Code(lang, text.slice(lineEnd, codeEnd))
		  })
		  
		  Expr.setStatic(function fromParsing(expr){
		      const car = Expr.Vector.forTrain(Expr.Train("eval", [], null))
		      const literal = {acc: "", closing: null}
		      const parser = {car, dotpath: "", expr, literal, pron: null,
		                      suffix: "", __proto__: Expr.Parser.prototype}
		      try {
		          var parsed = parser.parseExpr(expr)
		          const head = expr.meta?.head
		          if (head?.path === "$on") {
		              const train = (parsed.type === "train") ? parsed
		                          : Expr.Train("on", [parsed], null)
		              const lookup = Expr.Lookup(That, "$it", expr)
		              train.body.unshift(Expr.Keyarg(head.key, lookup, expr))
		              parsed = Expr.Holder("$", train, expr)
		          }
		          return parsed
		      } catch(er) {
		          return parser.error("unexpected error", er)
		      }
		  })
		  .setStatic(function fromRight(right, cellindex, lineindex = -1){
		      const expr = (right === "") ? null
		                                  : (lineindex < 0 ? Expr("", right)
		                                                   : Expr.Code.fromText(right))
		      return (cellindex > -1) ? expr.meta = Cell(cellindex, expr) : expr
		  })
		  
		  Expr.Value = {}
		  Expr.Value.holds = {
		      AND: Word("AND"), OR: Word("OR"), // add XOR
		      else: Word("else"), then: Word("then")
		  }
		  Expr.Value.prons = {
		      that: That, those: That,
		      this: This, these: This
		  }
		  Object.assign(Syn.keywords, Expr.Value.holds, Expr.Value.prons)
		  ```
		- Expression Parser
			- ```javascript
			  const Syn = logseq.Module.Synthesis
			  const Expr = Syn.Expression
			  const Norm = Syn.Normal
			  const Caller = Norm.forTypeOf("caller", Expr)
			  const Getter = Norm.forTypeOf("getter", Expr)
			  const Holds = Expr.Value.holds
			  const Let = Syn.keywords["let"].body
			  const On = Syn.keywords["on"].body
			  const Prons = Expr.Value.prons
			  const Rather = Syn.keywords["rather"].body
			  const Train = Norm.forTypeOf("train", Expr)
			  const Type = Syn.Type
			  const Word = Syn.Word
			  
			  function fixDashes(name){
			      return (name.startsWith("$")) ? name.replaceAll("-", "_") : name
			  }
			  
			  const Holder = Expr.Holder = function Holder(name, body, meta){
			      return Norm("holder", fixDashes(name), body, meta)
			  }
			  Holder.isNorm = Norm.isType.bind(null, "holder")
			  
			  const Keyarg = Expr.Keyarg = function Keyarg(name, body, meta){
			      return Norm("keyarg", fixDashes(name), body, meta)
			  }
			  Keyarg.isNorm = Norm.isType.bind(null, "keyarg")
			  
			  const Lookup = Expr.Lookup = function Lookup(head, name, meta){
			      const fix = (head === Word("that") &&
			                  typeof name === "string" && name.startsWith("$."))
			      return Norm("lookup", head, (fix ? name.replaceAll("-", "_") : name), meta)
			  }
			  Lookup.isNorm = Norm.isType.bind(null, "lookup")
			  
			  const Vector = Norm.forTypeOf("vector", Expr)
			  .setStatic(function forTrain(train){
			      return {args: [], keyarg: null, parts: [], train}
			  })
			  
			  Expr.Parser = function ExprParser(){}
			  .setProto(Syn.error)
			  
			  .setProto(function addMulti(arg){
			      const dotpath = this.dotpath
			      if (dotpath.length) {
			          arg = addMulti.processDotpath(dotpath, addMulti.kitAddGetter, arg)
			      }
			  
			      if (this.suffix) this.beginTrain(this.suffix, arg)
			      else this.addPart(".value", arg)
			      this.suffix = ""
			  }
			  .setStatic(function kitAddGetter(node){
			      const num = Number(node)
			      const isWord = isNaN(num)
			      const type = (isWord || num >= 0) ? "index" : "rindex"
			      this.res = Getter(type, isWord ? node : num, this.res)
			  })
			  .setStatic(function processDotpath(dotpath, kitAddGetter, arg){
			      const kit = {res: arg}
			      dotpath.split(".").forEach(kitAddGetter, kit)
			      return kit.res
			  })
			  )
			  
			  .setProto(function addPart(part, arg){
			      const car = this.car
			      const parts = car.parts
			      const word = (arg === undefined) ? part : arg.head
			      const keyarg = car.keyarg
			      if (keyarg === On) {
			          car.keyarg = Holder(word, On, this.expr)
			          return // done
			      } else if (keyarg === Let) {
			          car.keyarg = Keyarg(word, Let, this.expr)
			          return // done
			      } else if (!keyarg && parts.length < 1) {
			          if (word === Let || word === On) {
			              car.keyarg = word
			              return // done
			          } else if (word === Rather) {
			              car.keyarg = Holder(Rather, word, this.expr)
			              return // done
			          }
			      }
			  
			      var pre = parts[parts.length - 1]
			      if (Object.getOwn(Prons, pre)) {
			          const name = (arg === undefined) ? part :
			                       (Lookup.isNorm(arg) && arg.head.indexOf
			                        && arg.head.indexOf("-") > -1) ? arg.head : arg
			          arg = Lookup(Prons[pre], name, this.expr)
			          part = ".value"
			          parts.pop()
			          pre = parts[parts.length - 1]
			      }
			  
			      parts.push(part)
			      if (arg === undefined) return // done
			  
			      car.args.push(Holds[pre] ? Holder(pre, arg, this.expr) : arg)
			  })
			  .setProto(function assign(word){
			      return this.parseChain(Let) || this.parseChain(word)
			  })
			  
			  .setProto(function attemptLiteral(chain){
			      const literal = this.literal
			      const closing = literal.closing
			      const acc = literal.acc
			      literal.acc = acc + (acc ? " " : "") + chain
			      if (literal.acc === '"') return // continue
			      if (chain[chain.length - 1] !== closing) {
			          if (chain.length < 2) return // continue
			  
			          const suffix = chain.slice(-1)
			          const funcname = Expr.Parser.getFuncnameForSuffix(suffix)
			          if (!funcname) return // continue
			  
			          const lit = literal.acc
			          literal.acc = acc
			          const found = this.attemptLiteral(chain.slice(0, -1))
			          if (found) return found
			  
			          if (literal.acc.length) literal.acc = lit
			          else this.parseChain(suffix)
			          return // continue
			      }
			  
			      const lit = attemptLiteral.closeAcc(closing, literal.acc)
			      if (lit === undefined) return // continue
			  
			      this.addPart(Type.byjs[typeof lit].head, lit)
			      literal.acc = ""
			  }
			  .setStatic(function closeAcc(closing, acc){
			      if (closing === "'") return acc.slice(1, -1)
			      try { return JSON.parse(acc) } catch {}
			  })
			  )
			  
			  .setProto(function beginTrain(word, head){
			      this.car = Vector.forTrain(Train(head, [], this.car))
			  })
			  
			  .setProto(function carsFromCar(car){
			      return car.parts.map(carsFromCar.kitPart, {args: car.args, i: 0} )
			  }
			  .setStatic(function kitPart(part){
			      return (part.startsWith(".")) ? this.args[this.i++] : Word(part)
			  })
			  )
			  
			  .setProto(function dfltSuffix(word, suffix){
			      return this.parseChain(word) || this.parseChain(suffix)
			  })
			  .setProto(function dfltWord(word){
			      return this.addPart(word)
			  })
			  .setProto(function endCar(car){
			      car.train.body.push(this.loadForCar(car))
			  })
			  .setProto(function endTrain(word, forLast){
			      const car = this.car
			      const train = car.train
			      const head = train.head
			      const isCall = head &&
			          (typeof head !== "string" || head[0] !== head[0].toLowerCase())
			      if (!isCall || train.body.length) this.endCar(car)
			      else train.body = this.carsFromCar(car)
			  
			      var arg = (!isCall && train.body.length < 2) ? train.body[0]
			              : (!isCall) ? train
			              : Vector(Caller(train.body.length, train.head, this.expr),
			                       train.body, this.expr)
			      if (head === "$") arg = Holder("$", arg, this.expr)
			      if (!train.meta) {
			          if (forLast) return arg
			  
			          train.head = ""
			          train.meta = Vector.forTrain(Train("eval", [], null))
			      }
			      this.car = train.meta
			      this.addPart(".value", arg)
			  })
			  .setProto(function it(word){ return this.parseChain("?$it") })
			  .setProto(function loadForCar(car){
			      const keyarg = car.keyarg
			      const parts = car.parts
			      if (keyarg && parts.length < 1) this.parseChain("!" + keyarg.head)
			      const args = car.args
			      const expr = this.expr
			      const load = (parts.length !== 1) ?
			                   Vector(Lookup(parts.join("-"), parts, expr), args, expr) :
			                   (args.length > 0) ? args[0] :
			                   (typeof parts[0] !== "string") ? parts[0] :
			                   Lookup(parts[0], parts, this.expr)
			  
			      if (!keyarg) return load
			  
			      keyarg.body = load
			      return keyarg
			  })
			  .setProto(function nextCar(word){
			      this.endCar(this.car)
			      this.car = Vector.forTrain(this.car.train)
			  })
			  .setProto(function parseChain(chain){
			      if (!chain) return // continue // check negative
			  
			      const literal = this.literal
			      if (literal.acc.length) return this.attemptLiteral(chain)
			  
			      var funcname = Expr.Parser.getFuncnameForWord(chain)
			      if (funcname) return this[funcname](chain)
			  
			      literal.closing = Syn.closing[chain[0]]
			      if (literal.closing) return this.attemptLiteral(chain)
			  
			      const index = chain.indexOf("(")
			      if (index === 0) return this.parseChain("(") ||
			                              this.parseChain(chain.slice(1))
			      if (index > 0) {
			          this.suffix = "("
			          return this.parseChain(chain.slice(0, index)) ||
			                 this.parseChain(chain.slice(index + 1))
			      }
			  
			      const suffix = chain.slice(-1)
			      funcname = Expr.Parser.getFuncnameForSuffix(suffix)
			      if (funcname) return this[funcname](chain.slice(0, -1), suffix)
			  
			      if (chain.endsWith("'s")) return this.parseChain(chain.slice(0, -2)) ||
			                                       this.parseChain("its")
			  
			      const prefix = chain.slice(0, 1)
			      const firstWord = Expr.Parser.prefixes[prefix]
			      if (firstWord) return this.parseChain(firstWord) ||
			                            this.parseChain(chain.slice(1))
			  
			      const num = Number(chain)
			      return (!isNaN(num)) ? this.addPart(".num", num)
			           : this.parseChainHead(this.parseChainDotpath(chain))
			  })
			  .setProto(function parseChainDotpath(chain){
			      this.dotpath = ""
			      var index = 0
			      while (true) {
			          index = chain.indexOf(".", index + 1)
			          if (index < 0) return chain
			          if (chain[index - 1] !== "-") break
			      }
			      this.dotpath = chain.slice(index + 1)
			      return chain.slice(0, index)
			  })
			  .setProto(function parseChainHead(chain){
			      const hasDash = (chain.length > 1 && chain.indexOf("-") > -1)
			      const load = (hasDash) ? Lookup(chain, [], this.expr) : chain
			      if (this.dotpath || (this.suffix !== "")) this.addMulti(load)
			      else if (hasDash) this.addPart(".value", load)
			      else this.addPart(load)
			  })
			  .setProto(function parseExpr(expr){
			      const found = expr.body.split(" ").find(this.parseChain, this)
			      if (this.literal.acc) return this.error("bad literal", this.literal.acc)
			      if (found) return this.error("invalid", found)
			  
			      while (true) {
			          const last = this.endTrain(")", true)
			          if (last !== undefined) return last
			      }
			  })
			    
			  Expr.Parser.getFuncnameForSuffix = Object.getOwn.bind(null, {
			      "=": "assign",
			      ")": "dfltSuffix", ",": "dfltSuffix", ";": "dfltSuffix"
			  })
			  Expr.Parser.getFuncnameForWord = Object.getOwn.bind(null, {
			      "(": "beginTrain",
			      "=": "dfltWord", "<=": "dfltWord", ">=": "dfltWord",
			      ")": "endTrain",
			      it: "it", them: "it",
			      ")(": "nextCar", ",": "nextCar", ";": "nextCar"
			  })
			  Expr.Parser.prefixes = {
			      "!": Prons.this.body,
			      "?": Prons.that.body
			  }
			  ```
	- Page
	  collapsed:: true
		- ```javascript
		  const Syn = logseq.Module.Synthesis
		  const Norm = Syn.Normal
		  const Alist = Norm.forTypeOf("alist", Syn)
		  
		  Norm.prototype.toString.onType(function alist(){
		      return "alist{" + this.body + "}"
		  })
		  
		  const Block = Syn.Block = function Block(expr, head, block, meta){
		      return {head, expr, block, meta, __proto__: Block.prototype}
		  }
		  .addHasInst()
		  .setStatic(function forBlock(block){
		      return Block(null, null, block, Block.Meta(null))
		  })
		  .setStatic(function fromShort(short){
		      while (short && !short.block) { short = short.meta }
		      return short
		  })
		  .setStatic(function getDom(){
		      return {l: this.block ? null :
		                 document.getElementById("block-content-" + this.b.uuid),
		              b: (this.block) ? this.block : this.b, __proto__: Block.prototype}
		  })
		  
		  .setProto(function getHeadStr(){
		      const kit = {types: this.head.types, i: 0}
		      return "[ " + this.head.words.map(getHeadStr.kitWord, kit).join(" ") + " ]"
		  }
		  .setStatic(function kitWord(word){
		      return (word === ".") ? this.types[this.i++].head : word
		  })
		  )
		  
		  .setProto(function getOn(name){
		      return this.meta.on.dict[name]?.block
		  })
		  .setProto(function getThis(name){
		      return this.meta.this.dict[name]?.block || this.meta.parent?.getThis(name)
		  })
		  .setProto(function toString(){
		      return (this.expr) ? String(this.expr)
		           : " in " + Syn.pageBy(this.b.page.id).originalName
		  })
		  
		  Object.defineProperty(Block.prototype, "DOM", { get: Block.getDom })
		  
		  Block.Meta = function Meta(parent, children, these, on){
		      return {children: children || Block.Node(), on: on || Block.Node(),
		              parent, this: these || Block.Node()}
		  }
		  
		  const Node = Block.Node = function PhraseNode(level, _generic = []){
		      return {_generic, block: null, dict: {}, etc: false, level,
		              __proto__: PhraseNode.prototype}
		  }
		  .addHasInst()
		  .setProto(function add(val){
		      const old = this.block
		      if (Alist.isNorm(old)) old.body.push(val)
		      else this.block = (old) ? Alist(val.head, [old, val]) : val
		  })
		  .setProto(function getOrSet(key, level = 0){
		      return this.dict[key] ||= Block.Node(level)
		  })
		  
		  const Page = Syn.Page = function Page(page, parsetime){
		      return {name: page.originalName, synblock: Block.forBlock(page),
		              phrases: {byPath: Node(-1, null), byWords: Node(-1),
		                        length: 0, total: 0},
		              delegates: [], dependencies: [],
		              parsetime, __proto__: Page.prototype}
		  }
		  .addHasInst()
		  .setStatic(function nameBy(text){
		      text = String(text).trim()
		      const inbrackets = text.startsWith("[[") && text.endsWith("]]")
		      return ((inbrackets) ? text.slice(2,-2) : text).toLowerCase()
		  })
		  .setProto(function toString(){ return this.name })
		  
		  .setProto(function addPhrase(child){
		      const phrases = this.phrases
		      phrases.total += 1
		      if (!child) return
		  
		      const head = child.head
		      const path = head.path
		      if (path.startsWith("$") || !child.expr) return // invalid
		  
		      phrases.length += 1
		      const kit = {head, i: 0, node: phrases.byWords, path,
		                   pathDict: phrases.byPath.dict, pathStatus: "valid"}
		      head.words.forEach(addPhrase.kitAddNodeByWord, kit)
		      if (head.status.etc) kit.node.etc = true
		      kit.node.add(child)
		      if (kit.pathStatus === "invalid" || head.status.etc) return
		  
		      kit.node = phrases.byPath
		      addPhrase.kitAddNodeByWord.call(kit, path)
		      head.types.forEach(addPhrase.kitAddNodeByType, kit)
		      kit.node.add(child)
		  }
		  .setStatic(function kitAddNodeByType(type, index){
		      this.node = this.node.getOrSet(type.head, index)
		  })
		  .setStatic(function kitAddNodeByWord(word, index){
		      const generic = this.node._generic
		      const isGeneric = Array.isArray(generic)
		      if (word === ".") {
		          if (isGeneric) generic.push(this.path)
		          else this.pathStatus = "invalid"
		          word = this.head.types[this.i++].head
		      } else if (isGeneric) {
		          const pathDict = this.pathDict
		          generic.forEach( (path)=>{ delete pathDict[path] } )
		          this.node._generic = null
		      }
		      this.node = this.node.getOrSet(word, index)
		  })
		  )
		  
		  .setStatic(function fromParsing(name, logger, white){
		      return {curblock: null, logger, synpage: null, white,
		              __proto__: Syn.Page.Parser.prototype}.parseByName(name, true)
		  })
		  
		  const This = Syn.Word("this")
		  Page.Sign = {}
		  Page.Sign.prons = {
		      on: Syn.Word("on"),
		      some: Syn.Word("some"),
		      this: This,
		      these: This
		  }
		  Object.assign(Syn.keywords, Page.Sign.prons)
		  ```
		- Page Parser
			- ```javascript
			  REGEXP_DROP_PROPS = /\n?.*[:][:].*\n?/g
			  
			  const Syn = logseq.Module.Synthesis
			  const Block = Syn.Block
			  const Error = Syn.Error
			  const Expr = Syn.Expression
			  const Norm = Syn.Normal
			  const Page = Syn.Page
			  const No = Syn.Word("no")
			  
			  const Etc = Syn.keywords["etc."].body
			  const Prons = Page.Sign.prons
			  const Word = Syn.keywords["word"].body
			  
			  Page.Parser = function PageParser(){}
			  .setProto(Syn.error)
			  .setProto(function $delegate(text){
			      const delegates = this.synpage.delegates
			      text.split(", ").map(Syn.Page.nameBy).forEach(Array.appendOne, delegates)
			  })
			  .setProto(function $execute(text){
			      if (this.white) Expr.Code.exec(Expr.Code.fromText(text))
			  })
			  .setProto(function $include(text){
			      const curblock = this.curblock
			      this.curblock = null
			      this.$paste(text)
			      this.curblock = curblock
			  })
			  
			  .setProto(function $paste(text){
			      text.split(", ").map(Syn.Page.nameBy).forEach($paste.parse, this)
			  }
			  .setStatic(function parse(name){ // drop args
			      this.parseByName(name)
			  })
			  )
			  
			  .setProto(function parseBlockInSynblock(block, parent){
			      const content = block.content.replace(REGEXP_DROP_PROPS, "\n").trim()
			      if (content === "$omit" || content === "$enough") return content
			  
			      const lineindex = content.indexOf("\n")
			      const markindex = parseBlockInSynblock.markIndex(content, lineindex)
			      if (markindex === false) {
			          var spaceindex = content.indexOf(" ")
			          if (lineindex > -1 && lineindex < spaceindex) spaceindex = lineindex
			          if (spaceindex < 0) return // invalid
			  
			          const first = content.slice(0, spaceindex)
			          if (first.startsWith("$") && this[first]) {
			              this[first](content.slice(spaceindex + 1))
			          }
			          return // done
			      }
			      if (lineindex > -1 && lineindex !== markindex + 1) return // invalid
			  
			      const head = this.parseSign(content.slice(0, markindex), parent.head)
			      if (!head) return // invalid
			      if (Error.isNorm(head)) return // invalid
			  
			      const right = content.slice(markindex + 2).trim()
			      const isCell = (right.startsWith("{{cell") && right.endsWith("}}"))
			      const expr = Expr.fromRight((isCell ? right.slice(6, -2) : right),
			                                  (isCell ? 0 : -1), lineindex)
			      if (expr === undefined) return // invalid
			  
			      const child = Block(expr, head, block, Block.Meta(parent))
			      if (expr) expr.meta = child
			  
			      const name = (head.path.startsWith("$")) ? head.path.slice(1) : "children"
			      const node = parent.meta[name]
			      node.getOrSet(child.head.key).add(child)
			      return child
			  }
			  .setStatic(function markIndex(content, lineindex){
			      const markindex = content.indexOf(":")
			      if (markindex < 0) return content.startsWith(". ") && 0
			      if (lineindex > -1 && lineindex < markindex) return false
			  
			      const nextchar = content[markindex + 1]
			      return (nextchar === " " || !nextchar || nextchar === "\n") && markindex
			  })
			  )
			  
			  .setProto(function parseByName(name, isRoot){
			      const logger = this.logger.state.$log !== No && this.logger
			      const page = Syn.pageBy(name)
			      if (!page?.originalName) return // none
			  
			      const caller = (!isRoot) && this.synpage
			      const synpage = this.synpage = Syn.Page(page, Date.now())
			      const phrases = synpage.phrases
			      if (caller) {
			          phrases.byPath = caller.phrases.byPath
			          phrases.byWords = caller.phrases.byWords
			      }
			  
			      if (logger) {
			          logger.logParsingPage(synpage, isRoot)
			          logger.state.$eval_depth += 1
			          logger.indent(1)
			      }
			      this.curblock ||= synpage.synblock
			      Syn.blocksTreeOfPage(name).find(parseByName.traverseBlocksTree, this)
			      if (logger) {
			          logger.logParsedPage(phrases)
			          logger.state.$eval_depth -= 1
			          logger.indent(-1)
			      }
			  
			      if (caller) {
			          this.synpage = caller
			          const ds = caller.dependencies
			          if (!ds.find(parseByName.same, this)) ds.push(synpage)
			      }
			      return synpage
			  }
			  .setStatic(function same(dependency){
			      return dependency.name === this.synpage.name
			  })
			  .setStatic(function traverseBlocksTree(block){
			      if (Array.isArray(block)) return // invalid
			  
			      const parent = this.curblock
			      const child = this.parseBlockInSynblock(block, parent)
			      if (child === "$omit") return // done
			      if (child === "$enough") return child
			  
			      this.synpage.addPhrase(child)
			      this.curblock = child || this.synpage.synblock
			      const found = block.children.forEach(traverseBlocksTree, this)
			      this.curblock = parent
			      if (found === "$enough") return found
			  })
			  )
			  
			  .setProto(function parseSign(sign, head){
			      const status = (!head) ? {} : {...head.status}
			      const types = (!head) ? [] : head.types.slice()
			      const words = []
			      const kit = {parser: this, res: null, status, types, words}
			  
			      const fail = sign.split(" ").find(parseSign.kitWord, kit)
			      const pron = kit.res
			      if (fail) return pron
			  
			      if (pron && words.length > 2) return this.error("unexpected pron", pron)
			  
			      const key = (pron) ? words[0] : words.join("-")
			      const isSub = pron || !key ||
			          (words.length === 1 && words[0].indexOf("-") > -1 && words[0] !== "-")
			      const hasParent = head && !head.path.startsWith("$")
			      return {path: (isSub) ? ("$" + (pron || Prons.this).body) :
			                    (hasParent) ? head.path + "-" + key : key,
			              words: (isSub || !hasParent) ? words : [...head.words, ...words],
			              key, status, types}
			  }
			  .setStatic(function kitWord(signword){
			      if (signword.startsWith("**") && signword.endsWith("**")) {
			          signword = signword.slice(2, -2)
			      }
			  
			      const words = this.words
			      const pron = this.res
			      if (pron) {
			          if (pron === Prons.some) {
			              this.res = ""
			              this.types.push(Syn.Type.fromChain(signword))
			              words.push(".")
			          } else if (pron === Prons.this || pron === Prons.on) {
			              words.push(signword)
			          } else {
			              return this.res = this.parser.error("unexpected pron", pron)
			          }
			      } else {
			          const pron = this.res = Object.getOwn(Prons, signword)
			  
			          if (this.status.etc && pron !== Prons.this) {
			              return this.res = this.parser.error("word after " + Etc)
			          } else if (signword === Etc) {
			              this.status.etc = true
			              return // continue
			          } else if (pron) {
			              return words.length &&
			                     (pron === Prons.this || pron === Prons.on) &&
			                     (this.res = this.parser.error("unexpected 'this'"))
			          }
			  
			          words.push(signword)
			      }
			  })
			  )
			  ```
	- Evaluation
	  collapsed:: true
		- ```javascript
		  const Syn = logseq.Module.Synthesis
		  const Block = Syn.Block
		  const Expr = Syn.Expression
		  const Norm = Syn.Normal
		  const Page = Syn.Page
		  const Typed = Syn.Typed
		  const Word = Syn.Word
		  const No = Word("no")
		  const Void = Word("void")
		  
		  const Eval = Syn.Evaluation = function Eval(){}
		  .setStatic(function eval(obj){
		      const logger = obj.logger
		      const custom = {...logger.state.custom}
		      const state = Object.assign(logger.state, Eval.dfltState,
		          {$block: Typed("block", obj.block, ".block"), custom})
		      const evalion = {state, synpages: {}, logger, eid: obj.eid,
		                       __proto__: Eval.prototype}
		      const context = Eval.Context(null, null, [], [],
		                                   {evalion, $it: Void, state: state.custom})
		      const expr = Expr.fromRight(obj.what, obj.cellindex)
		      expr.meta = Block.forBlock(obj.block)
		      return evalion.evalExprInContext(expr, context, obj.type)
		  })
		  .setStatic(function is(val){
		      return val !== null && val !== undefined && val !== No && val !== Void
		  })
		  
		  .setProto(function error(msg, content){
		      this.logger.logError(msg, content)
		      return Syn.Error(msg)
		  })
		  
		  .setProto(function evalionError(msg, content, args){
		      return (args && args.find(evalionError.find, this)) ||
		             this.error(msg, ": " + content + " - in Evaluation")
		  }
		  .setStatic(function find(arg){
		      return Syn.Error.isNorm(arg) ||
		             (typeof arg === "string" && arg.includes("$ERR(") &&
		              this.error(arg, ": deeper - in Evaluation"))
		  })
		  )
		  
		  .setProto(function evalDepth(context, syn){
		      const state = this.state
		      const len = state.$eval_length
		      state.$eval_length = 0
		      const evaled = this.evalExprInContext(syn, context)
		      state.$eval_length = len
		      return evaled
		  })
		  
		  .setProto(function evalExprInContext(expr, context, type){
		      const block = Block.hasInst(expr) && expr
		      if (block) expr = block.expr
		      const logger = (this.state.$log !== No || type) && this.logger
		      const res = evalExprInContext.checkPlain(logger, expr) ??
		                  evalExprInContext.checkDepth.call(this, this.state)
		      if (res !== undefined) return res
		  
		      const outlen = logger && logger.out.length
		      if (logger) logger.indent(1).logEvalingExpr(expr, block, type)
		  
		      var evaled
		      try {
		          evaled = expr.evalIn(context)
		      } catch(er) {
		          evaled = this.evalionError("eval error", er)
		      }
		  
		      const next = evalExprInContext.next
		      const isProm = Promise.hasInst(evaled)
		      return (!isProm) ? next.call(this, expr, type, logger, outlen, evaled)
		           : evaled.then(next.bind(this, expr, type, logger, outlen))
		  }
		  .setStatic(function checkDepth(state){
		      state.$eval_depth += 1
		      const maxDepth = state.$eval_max_depth
		      if (state.$eval_depth <= maxDepth) return // done
		  
		      state.$eval_depth -= 1
		      return this.evalionError("max depth exceeded", maxDepth)
		  })
		  .setStatic(function checkPlain(logger, expr){
		      if (Norm.hasInst(expr)) return
		  
		      if (logger) logger.logPlainValue(expr)
		      return expr
		  })
		  .setStatic(function next(expr, type, logger, outlen, evaled){
		      this.state.$eval_depth -= 1
		      if (!logger) return evaled
		  
		      logger.indent(-1).logEvaled(evaled, type)
		      if (evaled !== expr)  return evaled
		  
		      logger.out.splice(outlen, logger.out.length)
		      logger.logPlainValue(evaled)
		      return evaled
		  })
		  )
		  
		  .setProto(function evalLength(syn, context){
		      const res = evalLength.checkLength.call(this, this.state)
		      if (res !== undefined) return res
		  
		      const evaled = syn.evalIn(context)
		      this.state.$eval_length -= 1
		      return evaled
		  }
		  .setStatic(function checkLength(state){
		      state.$eval_length += 1
		      const maxLength = state.$eval_max_length
		      if (state.$eval_length <= maxLength) return // done
		  
		      state.$eval_length -= 1
		      return this.evalionError("max length exceeded", maxLength)
		  })
		  )
		  
		  .setProto(function getBlockOfShort(short){
		      const block = Block.fromShort(short)
		      return (block.head) ? block : this.getPageBy(short).synblock
		  })
		  
		  .setProto(function getPageBy(by, ignore){
		      var name = by
		      if (typeof by !== "string") {
		          by = Block.fromShort(by)
		          const page = by && Syn.pageBy(by.block.page.id)
		          if (!page?.originalName) return // none
		  
		          name = page.name
		      }
		  
		      const cached = this.synpages[name]
		      if (cached) return cached
		  
		      var parsed = Syn.pages[name]
		      if (!parsed || getPageBy.updatedAfterThis.
		                     call(parsed.parsetime, parsed)) {
		          parsed = Syn.Page.fromParsing(name, this.logger,
		                                        name.startsWith("synthesis/"))
		          if (!parsed && this.state.$log !== No && !ignore) {
		              logger.logError("page not found", name)
		              return // none
		          }
		  
		          Syn.pages[name] = parsed
		      }
		      return this.synpages[name] = parsed
		  }
		  .setStatic(function updatedAfterThis(synpage){
		      const synblock = synpage.synblock
		      const page = synblock.block = Syn.pageBy(synblock.block.name)
		      return page.updatedAt > this ||
		             synpage.dependencies.find(updatedAfterThis, this)
		  })
		  )
		  
		  .setProto(function log(what, ...args){
		      const shouldLog = this.state.$log !== No || what === "logValue" ||
		            (this.logger.out.length < 2 && what === "logParsed")
		      if (shouldLog) this.logger[what](...args)
		  })
		  .setProto(function valForKey(val, key){
		      const func = Value[key]
		      return (func) ? func(val) : val
		  })
		  
		  const Value = Eval.Value = function Value(){}
		  .setStatic(function $page(val){ return Page.nameBy(val) })
		  .setStatic(function numOrDflt(dflt, val){
		      const num = Number(val)
		      return isNaN(num) ? dflt : num
		  })
		  .setStatic(function wordOrDflt(dflt, val){
		      return (Word.isNorm(val)) ? val : dflt
		  })
		  Value.$eval_max_depth = Value.numOrDflt.bind(null, 16)
		  Value.$eval_max_length = Value.numOrDflt.bind(null, 8)
		  
		  Eval.dfltState = {
		      $eval_depth: 0,
		      $eval_length: 0,
		      $eval_max_depth: 16,
		      $eval_max_length: 8,
		      $page: ""
		  }
		  ```
		- Logger
			- ```javascript
			  const Msg = logseq.Module.Msg
			  const Syn = logseq.Module.Synthesis
			  const Eval = Syn.Evaluation
			  const Word = Syn.Word
			  const No = Word("no")
			  const Yes = Word("yes")
			  
			  Eval.Logger = function Logger(state = {}){
			      return {state: Object.assign( {...Logger.dfltState} , state),
			              enabled: true, out: [], warnings: {}, __proto__: Logger.prototype}
			  }
			  .setProto(function indent(n){
			      const max = this.state.$log_depth
			      const next = (this.state.$eval_depth <= max)
			      if (!next && next !== this.enabled) this.out.push("\n" + "¦ ".repeat(max))
			      this.enabled = next
			  	return this
			  })
			  .setProto(function log(prefix, content, indent = 0){
			      if (!this.enabled) return this
			  
			      const out = this.out
			      if (prefix === "│" && out[out.length - 2]?.endsWith("┬")) return this
			  
			      var len = 0
			      if (prefix !== undefined) {
			          const times = Math.min(Math.max(1, this.state.$eval_depth + indent),
			                                 this.state.$eval_max_depth)
			          const pushed = "\n" + "│ ".repeat(times - 1) + prefix
			          len = pushed.length
			          out.push(pushed)
			      }
			      const logWidth = this.state.$log_width
			      if (typeof content ==="string" && content.length > logWidth - len) {
			          content = content.slice(0, logWidth - len - 4) + "..."
			      }
			      out.push(content)
			      return this
			  })
			  .setProto(function logBlock(block){
			      if (block && this.enabled) this.out.push(block.DOM)
			      return this
			  })
			  .setProto(function logError(msg, content){
			      const prev = this.enabled
			      this.enabled = true
			      this.log("├─X " + msg, content)
			      this.enabled = prev
			      this.warnings[msg] = (this.warnings[msg] || 0) + 1
			  })
			  
			  .setProto(function logEvaled(evaled, type){
			      if (this.state.$log_eval === No) return this
			  
			      const isProm = Promise.hasInst(evaled)
			      return (!isProm) ? logEvaled.next.call(this, type, evaled)
			           : evaled.then(logEvaled.next.bind(this, type))
			  }
			  .setStatic(function next(type, evaled){
			      const out = Word.isNorm(evaled) ? evaled.body : Syn.safeStringFor(evaled)
			      return (type) ? this.log("│", "").log("└", out)
			           : this.log("│", "", 1).log("├─┴o", out)
			  })
			  )
			  
			  .setProto(function logEvaledItem(evaled){
			      if (this.state.$log_eval === No) return this
			  
			      this.state.$eval_depth -= 1
			      return this.log("│", "", 1).indent(-1).
			             log("├─┴o", Syn.safeStringFor(evaled))
			  })
			  .setProto(function logEvalingExpr(expr, block, type){
			      if (this.state.$log_eval === No) return this
			  
			      const strExpr = Syn.stringFor(expr)
			      return (type) ? this.log(undefined, "┌ " + type + ": " + strExpr)
			           : this.log("│", "", -1).log("┌:", strExpr).logBlock(block)
			  })
			  .setProto(function logEvalingItem(item){
			      if (this.state.$log_eval === No) return this
			  
			      this.state.$eval_depth += 1
			      return this.indent(1).log("│", "", -1).log("┌·", Syn.safeStringFor(item))
			  })
			  .setProto(function logHolder(word){
			      this.log("│", "").log("├─¦", word)
			  })
			  .setProto(function logKeyarg(key, val){
			      const out = this.out
			      if (this.state.$log_eval === No || out.length < 2) return this
			      if (!out[out.length - 2].endsWith("o")) {
			          return this.logHolder(Syn.safeStringFor(val) + " » " + key)
			      }
			  
			      var content = out.pop()
			      const prefix = out.pop()
			      const logWidth = this.state.$log_width
			      const len = prefix.length + 3 + key.length
			      if (content.length > logWidth - len) {
			          content = content.slice(0, logWidth - len - 4) + "..."
			      }
			      out.push(prefix.slice(0, -1) + "O", content, " » " + key)
			      return this
			  })
			  .setProto(function logLookup(block){
			      return (!block || this.state.$log_lookup === No) ? this
			           : this.log("┌^^", block.getHeadStr(), 1).logBlock(block)
			  })
			  .setProto(function logNextCar(){
			      return (this.state.$log === No) ? this
			           : this.log("┴", "").log("┬", "")
			  })
			  .setProto(function logParsed(parsed){
			      if (this.state.$log_parse === No) return this
			  
			      const prefix = (this.out.length > 1 ? "└~~" : " └~~")
			        return this.log(prefix, Syn.stringFor(parsed), 1)
			  })
			  .setProto(function logParsedCode(code){
			      return (this.state.$log_parse === No) ? this
			           : this.log("└~~", Syn.stringForCode(code), 1)
			  })
			  .setProto(function logParsedPage(phrases){
			      if (this.state.$log_parse === No) return this
			  
			      return this.log("└+" + phrases.length + "/" + phrases.total, {phrases})
			  })
			  .setProto(function logParsingPage(synpage, isRoot){
			      if (this.state.$log_parse === No) return this
			  
			      if (isRoot) this.log("│", "")
			      return this.log("├─┬Z", {name: synpage.name})
			  })
			  .setProto(function logPlainValue(syn){
			      return (this.state.$log_eval === No) ? this
			           : this.log("│", "").log("├──o", Syn.safeStringFor(syn))
			  })
			  .setProto(function logSyn(syn, block){
			      return (this.state.$log_syn === No) ? this
			           : this.log("│", "").logLookup(block).log("├─=", Syn.safeStringFor(syn))
			  })
			  .setProto(function logValue(val){
			      this.log("│", "").log("├─>", Syn.safeStringFor(val))
			  })
			  .setProto(function print(){
			      if (this.state.$log === No) return this.out
			  
			      const arr = []
			      const warnings = this.warnings
			      for (const msg in warnings) {
			          const nof = warnings[msg]
			          arr.push(msg + (nof > 1 ? " x" + nof : ""))
			      }
			      if (arr.length) Msg.warning(arr.join(", "))
			      if (this.state.$log_target !== Word("block")) console.log(...this.out)
			      if (this.state.$log_target !== Word("console")) return this.out
			  })
			  
			  const Value = Eval.Value
			  Value.$log = Value.$log_eval = Value.$log_lookup =
			  Value.$log_parse = Value.$log_syn = Value.wordOrDflt.bind(null, Yes)
			  Value.$log_depth = Value.numOrDflt.bind(null, 8)
			  Value.$log_target = Value.wordOrDflt.bind(null, Word("both"))
			  Value.$log_visibility = Value.wordOrDflt.bind(null, Word("expand"))
			  Value.$log_width = Value.numOrDflt.bind(null, 80)
			  
			  Eval.Logger.dfltState = {
			      $log: Yes,
			      $log_eval: Yes,
			      $log_lookup: Yes,
			      $log_depth: 8,
			      $log_parse: Yes,
			      $log_syn: Yes,
			      $log_target: Word("both"),
			      $log_visibility: Word("expand"),
			      $log_width: 80
			  }
			  ```
		- Context
			- ```javascript
			  const Syn = logseq.Module.Synthesis
			  const Block = Syn.Block
			  const Eval = Syn.Evaluation
			  const Expr = Syn.Expression
			  const Holder = Expr.Holder
			  const Norm = Syn.Normal
			  const Rather = Syn.keywords["rather"].body
			  const Word = Syn.Word
			  const No = Word("no")
			  const Void = Word("void")
			  
			  var ctid = 1
			  
			  const Context = Eval.Context = function Context(lookup, synpage,
			                                                  params, args, parent){
			      return {syn: null, lookup, synpage, caller: synpage, params, args,
			              evalion: parent.evalion, $it: parent.$it, parent,
			              state: parent.state, _id: ctid++, __proto__: Context.prototype}
			  }
			  .addHasInst()
			  .setProto(function branch(cond, onIs, onIsnt){
			      if (Syn.Error.isNorm(cond)) return cond
			  
			      const res = (cond !== false && Eval.is(cond)) ? onIs : onIsnt
			      if (!res) return No
			  
			      this.evalion.log("logHolder", res.head)
			      return res
			  })
			  .setProto(function eval(syn, it){
			      if (it !== undefined) this.$it = it
			      return this.evalion.evalDepth(this, syn)
			  })
			  .setProto(function evalArg(arg, isCaller){
			      if (!Holder.isNorm(arg)) return this.eval(arg)
			  
			      if (this.state === this.parent.state) this.state = {...this.state}
			      return (isCaller) ? this.eval.bind(this, arg.body)
			           : Holder(arg.head, arg.body, this)
			  })
			  .setProto(function evalError(msg, content){
			      return this.evalion.evalionError(msg, content)
			  })
			  .setProto(function evalMsg(what, content){
			      if (!this.syn) return Void
			  
			      const node = this.syn.meta.on.dict[what]
			      if (!node) return this.parent.evalMsg(what, content)
			  
			      var expr = node.block.expr
			      if (Expr.isNorm(expr)) expr = expr.evalIn(this)
			      return this.eval(expr.body, content)
			  })
			  
			  .setProto(function evalSyn(syn){
			      const expr = evalSyn.getExpr.call(this, syn)
			      const isNorm = Norm.hasInst(expr)
			      return (isNorm) ? this.evalion.evalLength(expr, this) : expr
			  }
			  .setStatic(function getExpr(syn){
			      const isBlock = Block.hasInst(syn)
			      if (isBlock) {
			          for (key in syn.meta.on.dict) {
			              if (this.state === this.parent.state) this.state = {...this.state}
			              break
			          }
			      }
			      const expr = (isBlock) ? syn.expr : syn
			      this.evalion.log("logSyn", expr, isBlock && syn)
			      return expr
			  })
			  )
			  
			  .setProto(function getFunc(asfunc){
			      if (!Holder.isNorm(asfunc)) return asfunc
			  
			      const meta = asfunc.meta
			      return (!Expr.isNorm(meta)) ? meta.eval.bind(meta, asfunc.body)
			           : this.getFunc( this.evalArg(meta.evalIn(this)) )
			  })
			  .setProto(function getState(name){
			      if (name === "$context") return this
			  
			      const state = (name === "$it") ? this
			                  : (name.startsWith("$")) ? this.evalion.state : this.state
			      return Object.getOwn(state, name)
			  })
			  
			  .setProto(function getThat(lookup){
			      const body = lookup.body
			      const name = (Norm.hasInst(body)) ? String(body.evalIn(this)) : body
			      if (!this.syn) return this.getState(name)
			  
			      const argIndex = this.syn.head.types.findIndex(getThat.sameName, lookup)
			      if (argIndex > -1) return this.args[argIndex]
			  
			      const dashIndex = name.lastIndexOf("-")
			      const left = (dashIndex > -1) && name.slice(0, dashIndex)
			      const fromState = this.getState(name)
			      if (fromState !== undefined || !left) return fromState
			  
			      lookup.body = left
			      return getThat.call(this, lookup)
			  }
			  .setStatic(function sameName(type){
			      return type.body === this.body
			  })
			  )
			  
			  .setProto(function invokeCall(func, args, head){
			      return (head === "call") ? func.call(...args)
			           : (head === "bindct") ? func.bind(this, ...args)
			           : (head === "bind") ? func.bind(...args)
			           : this.evalError("unknown", head)
			  })
			  .setProto(function logValue(val){
			      const it = this.$it
			      this.evalion.log("logValue", val)
			      return it
			  })
			  .setProto(function resolve(synpage, evaled){
			      this.synpage = synpage
			      return (!Holder.isNorm(evaled)) ? evaled
			           : (evaled.head === Rather) ?
			                 this.evalion.evalLength(evaled.body, this)
			           : (!Expr.isNorm(evaled.meta)) ?
			                 evaled.meta.eval(evaled.body, this.$it)
			           : this.resolve(synpage, this.evalArg(evaled.meta.evalIn(this)) )
			  })
			  .setProto(function say(what, content){
			      const func = this.getFunc(this.getState(what.body))
			      return (Function.hasInst(func)) ? func.call(this, content)
			           : this.evalMsg(what.body, content)
			  })
			  .setProto(function toString(){ return this.synpage })
			  .setProto(function apply(){
			      const applier = {argI: 0, args: [], context: this, method: "", node: null,
			                       nodetype: "dflt", partI: 0, parts: this.lookup.body,
			                       syn: null, __proto__: Context.Applier.prototype}
			      return this.syn = applier.applyFromPage(this.synpage)
			  })
			  
			  ```
			- Context Applier
				- ```javascript
				  const Syn = logseq.Module.Synthesis
				  const Block = Syn.Block
				  const Context = Syn.Evaluation.Context
				  const Etc = Syn.keywords["etc."].body
				  const Expr = Syn.Expression
				  const Holder = Expr.Holder
				  const Lookup = Expr.Lookup
				  const Vector = Expr.Vector
				  const Type = Syn.Type
				  const Typed = Syn.Typed
				  const Word = Syn.Word
				  const No = Word("no")
				  
				  Context.Applier = function ContextApplier(){}
				  
				  .setProto(function applyFromPage(synpage){
				      this.applyOnPage(synpage)
				      if (!this.syn) synpage.delegates.find(applyFromPage.byDelegate, this)
				      if (!this.syn) applyFromPage.byDelegate.call(this, "synthesis/custom", true)
				      return this.syn || applyFromPage.byDelegate.call(this, "synthesis/base")
				  }
				  .setStatic(function byDelegate(delegate, ignore){
				      const synpage = this.context.evalion.getPageBy(delegate, ignore)
				      return synpage && this.applyOnPage(synpage)
				  })
				  )
				  
				  .setProto(function applyOnPage(synpage){
				      this.context.synpage = synpage
				      const phrases = synpage.phrases
				      return this.syn = applyOnPage.byPath.call(this, phrases.byPath) ||
				                        applyOnPage.byWords.call(this, phrases.byWords)
				  }
				  .setStatic(function byPath(node){
				      this.method = "matchArg"
				      node = this.node = node.dict[this.context.lookup.head]
				      return node && this.matchArg(node)?.block
				  })
				  .setStatic(function byWords(node){
				      this.method = "matchNode"
				      const found = this.matchNode(node)
				      if (!found) return // none
				  
				      const block = found.block
				      const nodetype = this.nodetype
				      if (nodetype === "dflt") {
				          this.context.args = this.args
				          return block
				      } else if (nodetype === "etc") {
				          this.context.args = this.args
				          this.context.synpage = this.context.caller
				          const plainArg = (this.parts.length === 2 && this.args.length === 1)
				          const etc = (plainArg) ? JSON.stringify(this.args[0])
				                    : this.parts.slice(found.level + 1).join(" ")
				          const expr = Expr("$dynamic", block.expr.body.replace(Etc, etc))
				          return expr.meta = Block(expr, block.head, block.block, block.meta)
				      } else if (Expr.Lookup.isNorm(nodetype)) {
				          this.context.synpage = this.context.caller
				          const expr = this.context.lookup.meta
				          const parts = [...this.parts.slice(0, this.partI - 1), ".value"]
				          const lookup = Lookup(parts.join("-"), parts, expr)
				          var args = this.context.args
				          args = [...args.slice(0, this.argI - 1),
				                  Vector(nodetype, args.slice(this.argI - 1), expr)]
				          return Block(Vector(lookup, args, expr),
				                       block.head, block.block, block.meta)
				      }
				  })
				  )
				  
				  .setProto(function matchArg(node){
				      if (this.argI >= this.context.args.length) return node
				  
				      const arg = this.context.args[this.argI++]
				      const isTyped = Typed.isNorm(arg)
				      if (this.method === "matchNode") {
				          const nondot = (typeof arg === "number") ? arg
				                       : isTyped && arg.meta === ".word" && arg.body
				          const node = (nondot !== undefined &&
				                        Object.getOwn(this.node.dict, nondot))
				          const found = node && this.matchNode(node)
				          if (found) return found
				      }
				  
				      this.args.push(arg)
				      const found = (isTyped) ? this.matchValOfType(arg.body, arg.meta)
				                              : this.matchUntypedArg(arg)
				      if (found) return found
				  
				      this.args.pop()
				      this.argI -= 1
				  })
				  .setProto(function matchKey(key){
				      const node = Object.getOwn(this.node.dict, key)
				      if (!node) return // none
				  
				      const found = this[this.method](node)
				      if (found) return found
				      if (!node.block) return // none
				      if (key !== ".value") return // none
				      if (this.partI < 2) return // invalid
				  
				      const parent = this.context
				      const parts = this.parts.slice(this.partI - 1)
				      const lookup = Lookup(parts.join("-"), parts, parent.lookup.meta)
				      const context = Context(lookup, parent.caller,
				                              parent.params.slice(this.argI - 1),
				                              parent.args.slice(this.argI - 1), parent)
				      if (!context.apply()) return // none
				  
				      this.nodetype = lookup
				      return node
				  })
				  .setProto(function matchNode(node){
				      if (this.partI >= this.parts.length) return node.block && node
				  
				      const prevNode = this.node
				      this.node = node
				      const part = this.parts[this.partI++]
				      const found = (part.startsWith(".")) ? this.matchArg()
				                  : (this.matchKey(part) || this.matchWordArg(part))
				      if (found) return found
				      if (node.etc) {
				          this.args.push(...this.context.args.slice(this.argI))
				          this.nodetype = "etc"
				          return node
				      }
				  
				      this.partI -= 1
				      this.node = prevNode
				  })
				  .setProto(function matchUntypedArg(arg){
				      const type = Type.of(arg)
				      return (type === Type.Num && this.matchKey(arg)) ||
				             (Holder.isNorm(arg) && Context.hasInst(arg.meta) &&
				              this.matchKey(".func")) ||
				             this.matchKey(type.head) || this.matchKey(".value")
				  })
				  .setProto(function matchValOfType(val, type){
				      return (type === ".word" && this.matchKey(val)) || this.matchKey(type) ||
				             this.matchKey(Type.of(val).head) || this.matchKey(".value")
				  })
				  .setProto(function matchWordArg(part){
				      this.args.push(Word(part))
				      const found = this.matchValOfType(part, ".word")
				      if (found) return found
				      else this.args.pop()
				  })
				  ```
	- Evaluate Normal in Context
	  collapsed:: true
		- ```javascript
		  const Module = logseq.Module
		  const Syn = Module.Synthesis
		  const Context = Syn.Evaluation.Context
		  const Expr = Syn.Expression
		  const Holder = Expr.Holder
		  const Norm = Syn.Normal
		  const CodeFunc = Norm.forTypeOf("code_func")
		  const Rather = Syn.keywords["rather"].body
		  const Typed = Syn.Typed
		  const Word = Syn.Word
		  const No = Word("no")
		  const On = Word("on")
		  const That = Word("that")
		  const This = Word("this")
		  const Void = Word("void")
		  
		  function evalIn(context){
		      const f = evalIn.forType[this.type]
		      return (f) ? f.call(this, context)
		           : context.evalError("unexpected", String(this))
		  }
		  Norm.setProto(evalIn)
		  
		  evalIn.onType(function alist(context){
		      const isCaller = (this.head.type === "caller" &&
		                        this.head.body.body !== "branch")
		      const kit = {args: [], context, i: 0, items: this.body, isCaller}
		      return alist.kitNext.call(kit)
		  }
		  .setStatic(function kitNext(val){
		      if (val !== undefined) {
		          if (Typed.isNorm(val) && val.meta === ".args") {
		              const body = val.body
		              if (Array.isArray(body)) this.args.push(...body)
		              else this.args.push(body)
		          } else {
		              this.args.push(this.isCaller ? this.context.getFunc(val) : val)
		          }
		      }
		      if (this.i >= this.items.length) return this.args
		  
		      const item = this.items[this.i++]
		      const evaled = this.context.evalArg(item, this.isCaller)
		      const isProm = Promise.hasInst(evaled)
		      return (!isProm) ? kitNext.call(this, evaled)
		           : evaled.then(kitNext.bind(this))
		  })
		  )
		  
		  evalIn.onType(function caller(context, params, args){
		      const evalion = context.evalion
		      const body = this.body
		      const evaled = (body.evalIn) ? body.evalIn(context) :
		          (!body.startsWith(".") && body.indexOf("-.") < 0) ?
		          (Object.getOwn(Syn, body) || Object.getOwn(Module, body)) :
		          "to be implemented" // get func by id
		  
		      const type = Norm.hasInst(evaled) && evaled.type
		      const func = (type) ? evaled.body : evaled
		      if (!Function.hasInst(func)) {
		          if (Syn.Error.isNorm(func)) return func
		          if (evalion.synpages["synthesis/base"]) {
		              return context.evalError("call of non-func", func)
		          }
		  
		          evalion.getPageBy("synthesis/base")
		          return caller.call(this, context, params, args)
		      }
		  
		      context.synpage ||= evalion.getPageBy(this) // for top-level js
		      evalion.log("logSyn", func)
		      try {
		          return (type !== "call") ? func.call(context, ...args)
		               : context.invokeCall(func, args, evaled.head)
		      } catch(er) {
		          return context.evalError("failed call", er.message)
		      }
		  })
		  .onType(function cell(context){
		      const span = this.meta.DOM.DOM.l?.querySelectorAll(".cell")[this.head]
		      if (span) {
		          if (span.syn?.eid === context.evalion.eid) return span.syn.val
		  
		          const text = span.querySelector("input").value
		          if (text !== " :cell") {
		              const res = Expr("cell ", text, this).evalIn(context)
		              Syn.Span.setCell(span, context.evalion.eid, res)
		              return res
		          }
		      }
		      return this.body.evalIn(context)
		  })
		  
		  evalIn.onType(function code(context){
		      context.evalion.log("logParsedCode", this.body)
		      try {
		          return Expr.Code.exec(this).then(code.next.bind(this, context))
		      } catch(er) {
		          return context.evalError("failed call", er.message)
		      }
		  }
		  .setStatic(function next(context, evaled){
		      if (!Function.hasInst(evaled)) {
		          this.type = "code_script"
		          return evaled
		      }
		  
		      this.meta.expr = CodeFunc(this.head._key, evaled, this)
		      return evaled.call(context, ...context.args)
		  })
		  )
		  
		  evalIn.onType(function code_func(context){
		      try {
		          return this.body.call(context, ...context.args)
		      } catch(er) {
		          return context.evalError("failed call", er.message)
		      }
		  })
		  .onType(function code_script(context){
		      return this.head.eval(this.body)
		  })
		  .onType(function error(context){
		      return context.evalion.error(this.head, this.body)
		  })
		  .onType(function expr(context){
		      const parsed = this.meta.expr = Expr.fromParsing(this)
		      if (context.args.length && Expr.Vector.isNorm(parsed)
		                              && this.head === "$dynamic") {
		          parsed.body.push(...context.args.slice(this.meta.head.types.length))
		      }
		      context.evalion.log("logParsed", parsed)
		      const isNorm = Norm.hasInst(parsed)
		      return (isNorm) ? parsed.evalIn(context) : parsed
		  })
		  
		  evalIn.onType(function getter(context){
		      const meta = this.meta
		      const parent = (meta === "$") ? context
		                   : (typeof meta === "string") ?
		                     (Object.getOwn(Syn, meta) || Object.getOwn(Module, meta))
		                   : Expr.Getter.isNorm(meta) ? getter.call(meta, context)
		                   : context.evalSyn(meta)
		      var evaled = getter.eval(parent, this.head, this.body)
		      return (evaled === undefined) ? null: evaled
		  }
		  .setStatic(function eval(parent, type, value){
		      if (parent === null || parent === undefined) return
		      if (type === "rindex") return parent[(parent.length || 0) - value]
		      if (type === "index") {
		          return (!value.startsWith("$")) ? parent[value]
		               : Norm("call", value.slice(1), parent)
		      }
		  })
		  )
		  
		  evalIn.onType(function holder(context){
		      return (this.head === Rather || !Expr.isNorm(this.meta)) ? this
		           : context.evalArg(this)
		  })
		  .onType(function keyarg(context){ return Void })
		  
		  evalIn.onType(function lookup(context, params, args){
		      const head = this.head
		      if (head === "") return Void
		  
		      const body = this.body
		      const evalion = context.evalion
		      if (!args) {
		          const block = evalion.getBlockOfShort(this)
		          if (head === That) return context.getThat(this) ?? Void
		  
		          const isSub = (head === This || head === On)
		          const sub = (head === This) ? block.getThis(body)
		                    : (head === On) ? block.getOn(body)
		                    : (block.getOn(head) || block.getThis(head))
		          if (sub !== undefined) return context.evalSyn(sub)
		          if (isSub) return context.evalError(head.body + " phrase not found",
		                                              body)
		      }
		  
		      const log = params && !params.length
		      if (log) evalion.log("logEvalingItem", args[0])
		  
		      if (evalion.state.$page) {
		          context.synpage = evalion.getPageBy(evalion.state.$page)
		          evalion.state.$page = ""
		      }
		      const synpage = context.synpage || evalion.getPageBy(this)
		      context = Context(this, synpage, params || [], args || [], context)
		      const syn = context.apply()
		      if (!syn) return (body.length === 1) ? Word(body[0])
		                     : evalion.evalionError("matching phrase not found",
		                                            "{ " + head + " }", args)
		  
		      const evaled = context.evalSyn(syn)
		      const isProm = Promise.hasInst(evaled)
		      return (!isProm) ? lookup.resolve(log, context, synpage, evaled)
		           : evaled.then(lookup.resolve.bind(null, log, context, synpage))
		  }
		  .setStatic(function resolve(log, context, synpage, evaled){
		      const resolved = context.resolve(synpage, evaled)
		      if (!log) return resolved
		  
		      const isProm = Promise.hasInst(resolved)
		      return (!isProm) ? resolve.log(context, resolved)
		           : resolved.then(resolve.log.bind(null, context))
		  }
		  .setStatic(function log(context, resolved){
		      context.evalion.log("logEvaledItem", resolved)
		      return resolved
		  })
		  )
		  )
		  
		  const Train = function Train(){}
		  .setProto(function assign(key, val){
		      const evalion = this.context.evalion
		      const isCustom = !key.startsWith("$")
		      const state = (key === "$it") ? this.context
		                  : (!isCustom) ? evalion.state : this.context.state
		  
		      const prev = state[key]
		      state[key] = (isCustom) ? val : evalion.valForKey(val, key)
		      evalion.log("logKeyarg", key.replaceAll("_", "-"), val)
		  
		      const evaled = this.next(true, val)
		      const isProm = Promise.hasInst(evaled)
		      return (!isProm) ? this.close(state, key, prev, evaled)
		           : evaled.then(this.close.bind(this, state, key, prev))
		  })
		  .setProto(function close(state, key, prev, evaled){
		      if (key !== "$it") state[key] = prev
		      return evaled
		  })
		  .setProto(function next(assigned, val){
		      const context = this.context
		      if (!assigned && val !== undefined) {
		          context.$it = val
		          context.evalion.log("logKeyarg", "$it")
		      }
		      context.evalion.log("logNextCar")
		      if (this.i >= this.cars.length) return val
		  
		      const car = this.cars[this.i++]
		      const type = (Expr.Keyarg.isNorm(car)) ? "keyarg"
		                 : (Expr.Holder.isNorm(car)) ? "holder"
		                 : "dflt"
		      const head = (type !== "dflt") && car.head
		      const evaled = context.evalArg(type === "keyarg" ? car.body : car)
		      const func = (head) ? this.assign : next
		      const isProm = Promise.hasInst(evaled)
		      return (!isProm) ? func.call(this, head, evaled)
		           : evaled.then(func.bind(this, head))
		  })
		  
		  evalIn.onType(function train(context){
		      const train = {cars: this.body, context, i: 0, __proto__: Train.prototype}
		      return train.next(true)
		  })
		  .onType(function typed(context){ return this })
		  .onType(function vector(context){
		      const evalType = this.evalIn.forType
		      const args = evalType.alist.call(this, context)
		      const head = this.head
		      const eval = evalType[head.type]
		      const isProm = Promise.hasInst(args)
		      return (!isProm) ? eval.call(head, context, this.body, args)
		           : args.then(eval.bind(head, context, this.body))
		  })
		  ```