<script>
	import { flip } from 'svelte/animate';
  
	let applicants = [
	  {
		"Heading": "CV shared",
		"names": ["Chandu", "Sai"]
	  },
	  {
		"Heading": "Shortlisted",
		"names": ["Gopi", "Charan"]
	  },
	  {
		"Heading": "Rejected",
		"names": ["Rajesh"]
	  }
	];
  
	let hoveringOverApplicant;
  
	function dragStart(event, applicantIndex, nameIndex) {
	  const data = { applicantIndex, nameIndex };
	  event.dataTransfer.setData('text/plain', JSON.stringify(data));
	}
  
	function drop(event, applicantIndex) {
	  event.preventDefault();
	  const json = event.dataTransfer.getData("text/plain");
	  const data = JSON.parse(json);
  
	  const [name] = applicants[data.applicantIndex].names.splice(data.nameIndex, 1);
  
	  applicants[applicantIndex].names.push(name);
	  applicants = applicants;
  
	  hoveringOverApplicant = null;
	}
  </script>
  
  <p>Candidate Status</p>
  
  {#each applicants as applicant, applicantIndex (applicant)}
	<div animate:flip>
	  <b>{applicant.Heading}</b>
	  <ul
		class:hovering={hoveringOverApplicant === applicant.Heading}
		on:dragenter={() => hoveringOverApplicant = applicant.Heading}
		on:dragleave={() => hoveringOverApplicant = null}
		on:drop={event => drop(event, applicantIndex)}
		ondragover="return false"
	  >
		{#each applicant.names as name, nameIndex (name)}
		  <div class="item" animate:flip>
			<li
			  draggable={true}
			  on:dragstart={event => dragStart(event, applicantIndex, nameIndex)}
			>
			  {name}
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
	  color: white;
	}
	ul {
	  border: solid lightgray 1px;
	  display: flex;
	  height: 40px;
	  padding: 10px;
	}
  </style>
  