<script>
  import { flip } from 'svelte/animate';

  let applicants = [
    {
      "Heading": "CV shared",
      "names": ["Chandu", "Sai", "Hari", "Muni", "Raghu"]
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
    applicants = [...applicants]; // Make a copy to trigger reactivity

    hoveringOverApplicant = null;
  }

  function editName(applicantIndex, nameIndex) {
    const newName = prompt("Enter a new name:");
    if (newName) {
      applicants[applicantIndex].names[nameIndex] = newName;
      applicants = [...applicants]; // Make a copy to trigger reactivity
    }
  }

  function deleteName(applicantIndex, nameIndex) {
    applicants[applicantIndex].names.splice(nameIndex, 1);
    applicants = [...applicants]; // Make a copy to trigger reactivity
  }
</script>

<h1 style="color:blue;">Candidate Status</h1>

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
            <button on:click={() => editName(applicantIndex, nameIndex)}>Edit</button>
            <button on:click={() => deleteName(applicantIndex, nameIndex)}>Delete</button>
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
    height: 60px;
    padding: 10px;
  }
</style>
