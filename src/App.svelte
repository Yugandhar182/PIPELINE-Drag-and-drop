<script>
  import { flip } from 'svelte/animate';

  let applicants = [
    {
      "name": "CV shared",
      "items": ["Chandu", "Sai"]
    },
    {
      "name": "Shortlisted",
      "items": ["Gopi", "Charan"]
    },
    {
      "name": "Rejected",
      "items": ["Rajesh"]
    }
  ];

  let hoveringOverApplicant;

  function dragStart(event, applicantIndex, itemIndex) {
    const data = { applicantIndex, itemIndex };
    event.dataTransfer.setData('text/plain', JSON.stringify(data));
  }

  function drop(event, applicantIndex) {
    event.preventDefault();
    const json = event.dataTransfer.getData("text/plain");
    const data = JSON.parse(json);

    const [item] = applicants[data.applicantIndex].items.splice(data.itemIndex, 1);

    applicants[applicantIndex].items.push(item);
    applicants = applicants;

    hoveringOverApplicant = null;
  }
</script>

<p>Candidate Status</p>

{#each applicants as applicant, applicantIndex (applicant)}
  <div animate:flip>
    <b>{applicant.name}</b>
    <ul
      class:hovering={hoveringOverApplicant === applicant.name}
      on:dragenter={() => hoveringOverApplicant = applicant.name}
      on:dragleave={() => hoveringOverApplicant = null}
      on:drop={event => drop(event, applicantIndex)}
      ondragover="return false"
    >
      {#each applicant.items as item, itemIndex (item)}
        <div class="item" animate:flip>
          <li
            draggable={true}
            on:dragstart={event => dragStart(event, applicantIndex, itemIndex)}
          >
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
    color: white;
  }
  ul {
    border: solid lightgray 1px;
    display: flex;
    height: 40px;
    padding: 10px;
  }
</style>
