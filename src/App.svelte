<script>
  let qaData = {};

  function handleFileChange(event) {
    const file = event.target.files[0];
    const reader = new FileReader();

    reader.onload = () => {
      try {
        qaData = JSON.parse(reader.result);
      } catch (error) {
        console.error(error);
      }
    };

    reader.readAsText(file);
  }

  function handleEditQuestion(id) {
    const newQuestion = prompt("Enter new question");
    console.log(newQuestion);
    let data = {
      "text" :newQuestion
    }
    if (newQuestion) {
      fetch("https://pa-rephrase.onrender.com/api", {
        method: "POST",
    mode: "cors",
    cache: "no-cache",
    credentials: "same-origin",
    headers: {
      "Content-Type": "application/json",
    },
    redirect: "follow", 
    referrerPolicy: "no-referrer", 
    body: JSON.stringify(data), // body data type must match "Content-Type" header
  })
  .then(res=> res.text())
        .then((data) => {
          data = JSON.parse(data)
          console.log(data)
          qaData[id].question = newQuestion;
          qaData[id].answer = data.message;
        })
        .catch((error) => console.error(error));
    }
  }
</script>

<main>
  <h1>ExportGPT</h1>
  <input type="file" on:change={handleFileChange} />
  <div id="qaContainer">
    {#each Object.entries(qaData) as [id, qa]}
      <div class="qaCard">
        <h2>{qa.question}</h2>
        <p>{qa.answer}</p>
        <button on:click={() => handleEditQuestion(id)}>Edit</button>
      </div>
    {/each}
  </div>
</main>

<style>
  body {
    font-family: Arial, sans-serif;
    margin: 0;
    padding: 0;
  }

  h1 {
    text-align: center;
    margin: 1rem 0;
  }

  input[type="file"] {
    margin: 1rem;
  }

  #qaContainer {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
  }

  .qaCard {
    width: calc(50% - 1rem);
    margin: 0.5rem;
    padding: 1rem;
    border: 1px solid #ccc;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  }

  .qaCard h2 {
    margin-top: 0;
  }

  .qaCard button {
    margin-top: 1rem;
  }
</style>
