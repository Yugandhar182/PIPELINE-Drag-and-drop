<script>
	import { flip } from 'svelte/animate';
  
	let CANDIDATES = [
	  {
		"name": "Applied candidates",
		"items": ["Sai", "Chandu", "Dilli", "Charan", "Gopi", "Koti", "Vinoth"]
	  },
	  {
		"name": "Selected Candiadates",
		"items": []
	  },
	  {
		"name": "Rejected Candidates",
		"items": []
	  }
	];
  
	let hoveringOverCANDIDATES;
	let dropIndex = null;
  
	function dragStart(event, CANDIDATESIndex, itemIndex) {
	  // Set the custom data transfer format
	  event.dataTransfer.setData('text/plain', `${CANDIDATESIndex}:${itemIndex}`);
	}
  
	function drop(event, CANDIDATESIndex) {
	  event.preventDefault();
	  const [sourceIndex, itemIndex] = event.dataTransfer.getData('text/plain').split(':');
  
	  const [item] = CANDIDATES[sourceIndex].items.splice(itemIndex, 1);
  
	  const items = CANDIDATES[CANDIDATESIndex].items;
	  items.splice(dropIndex ?? items.length, 0, item);
	  CANDIDATES = CANDIDATES;
  
	  hoveringOverCANDIDATES = null;
	}
  
	function dragOver(e) {
	  dropIndex = null;
	  const itemElement = e.target.closest('[data-item]');
	  if (itemElement == null)
		return;
  
	  const { CANDIDATES, item } = itemElement.dataset;
	  const targetCANDIDATES = CANDIDATES.find(c => c.name == CANDIDATES);
	  dropIndex = targetCANDIDATES.items.indexOf(item);
	}
  </script>
  
  <p>CANDIDATES Status</p>
  
  {#each CANDIDATES as CANDIDATES, CANDIDATESIndex (CANDIDATES)}
	<div animate:flip>
	  <b>{CANDIDATES.name}</b>
	  <ul
		class:hovering={hoveringOverCANDIDATES === CANDIDATES.name}
		on:dragenter={() => hoveringOverCANDIDATES = CANDIDATES.name}
		on:dragleave={() => hoveringOverCANDIDATES = null}
		on:drop={event => drop(event, CANDIDATESIndex)}
		on:dragover|preventDefault={dragOver}>
		{#each CANDIDATES.items as item, itemIndex (item)}
		  <div class="item" data-CANDIDATES={CANDIDATES.name} data-item={item} animate:flip>
			<li draggable={true} on:dragstart={event => dragStart(event, CANDIDATESIndex, itemIndex)}>
			  {item}
			</li>
		  </div>
		{/each}
	  </ul>
	</div>
  {/each}
  
  <style>
	.hovering {
	  border-color: orange;
	}
	.item {
	  display: inline;
	}
	li {
	  background-color: lightgray;
	  cursor: pointer;
	  display: inline-block;
	  margin-right: 10px;
	  padding: 10px;
	}
	li:hover {
	  background: orange;
	  color: rgb(224, 15, 15);
	}
	ul {
	  border: solid lightgray 1px;
	  display: flex;
	  height: 40px;
	  padding: 10px;
	}
  </style>
  
  
