template:: reference property in the page
template-list-as:: view
template-usage:: `{|}, :default "—", :deep-search ""`
arg-default::
arg-deep-search?:: no
arg-macro-mode?:: no
template-version:: 2
plugin-version:: 4.0.0
link:: [Showroom](https://github.com/stdword/logseq13-full-house-plugin/discussions/37)

	- ```typescript
	  ``{
	      var blocks = await logseq.Editor.getPageBlocksTree(c.page.name)
	      var layers = [
	        [c.block],
	        [c.page],
	        blocks.map(dev.context.block),
	        blocks.map((b) => b.children.map(dev.context.block)).flat(),
	        // the last layer is all other blocks
	      ]
	      var name = c.args.$1 || c.args.name
	      if (!name)
	        return ''
	  
	      for (var layer of layers) {
	        for (var entity of layer) {
	          var value = entity.props[name]
	          if (value !== undefined)
	            return value
	        }
	      }
	  
	      var defaultValue = c.args.default || ''
	      if (c.args['macro-mode'])
	        if (defaultValue.startsWith('$'))
	          defaultValue = c.template.props['arg-default']
	      var values = [defaultValue]
	  
	      if (c.args['deep-search'])
	        await dev.tree.walk(
	          {content: '', children: blocks.slice(1)},
	          (b, level) => {
	            if (level < 1 || b.uuid === c.block.uuid)
	              return
	            var p = (b.properties || {})[name];
	            if (p !== undefined)
	              values.unshift(p)
	          }
	        )
	  
	      return values[0]
	  }``
	  ```