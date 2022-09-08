<script>
  // @ts-nocheck

  import witchpic from "../src/assets/CartoonWitch.jpg";
  import { createForm } from "svelte-forms-lib";

  let checked = "";
  let checkedmodal ="";
  let dataperson = [];
  let processing = false;
  let statusAlert = false;
  let statusError = false;
  let average =0.0;

  async function getAverage() {
    processing = true;

    const res = await fetch(
      "https://witch-app-udindev.herokuapp.com/witch/kills/average",
      {
        method: "POST",
        headers: {
          Accept: "application/json",
          "Content-Type": "application/json",
        },
        body: JSON.stringify(dataperson),
      }
    );

    //const data = await res.json();

    if (res.ok) {
      const data = await res.json();
      statusAlert = true;
      processing = false;
      statusError = false;
      dataperson = [];
      dataperson = data["villagePerson"];
      average = data["average"];
      dataperson = dataperson;
      console.log(dataperson);
      //handleClick();
    } else {
      handleModal();
      statusAlert = false;
      processing = false;
      statusError = true;
      return { status: "NOK" };
    }
  }

  function handleClick() {
    checked == "checked" ? (checked = "") : (checked = "checked");
  }

  function handleModal() {
    checkedmodal == "checked" ? (checkedmodal = "") : (checkedmodal = "checked");
  }

  const { form, errors, state, handleChange, handleSubmit } = createForm({
    initialValues: {
      personName: "",
      ageOfDeath: 0,
      yearOfdeath: 0,
      peopleKilled : 0,
      bornOnYear : 0
    },
    validate: (values) => {
      let errs = {};
      if (values.personName === "") {
        errs["personName"] = "Name is required";
      }

      if (values.ageOfDeath === 0) {
        errs["ageOfDeath"] = "Age Of Death is required";
      }

      if (values.ageOfDeath <= 0) {
        errs["ageOfDeath"] = "invalid value for age of death";
      }

      if (values.yearOfdeath === 0) {
        errs["yearOfdeath"] = "Year Of Death is required";
      }

      if (values.yearOfdeath <= 0) {
        errs["yearOfdeath"] = "invalid value for age of death";
      }

      if (values.ageOfDeath >= values.yearOfdeath)  {
        errs["ageOfDeath"] = "Age Of Death cannot be greater than or equal to Year Of Death";
      }

      return errs;
    },
    onSubmit: (values) => {
      handleClick();
      dataperson.push(values);
      dataperson = dataperson;
    },
  });
</script>


<input type="checkbox" id="my-modal-3" class="modal-toggle" on:click="{handleModal}" bind:checked={checkedmodal}/>
<div class="modal">
  <div class="modal-box relative">
    <label for="my-modal-3" class="btn btn-sm btn-circle absolute right-2 top-2">✕</label>
    <h3 class="text-lg font-bold">Something Wrong !</h3>
    <p class="py-4">Task failed successfully. the witch has run away</p>
  </div>
</div>


<div class="drawer drawer-end">
  <input id="my-drawer-4" type="checkbox" class="drawer-toggle" bind:checked />
  <div class="drawer-content">
    <div class="hero min-h-screen bg-base-200">
      <div class="hero-content flex-col lg:flex-row">
        <img
          alt="witchpic"
          src={witchpic}
          class="max-w-sm rounded-lg shadow-2xl"
        />
        <div>
          <h1 class="text-5xl font-bold">Witch App</h1>
          <p class="py-2 text-4xl">exorcising witch begins</p>

          <p class="py-2 text-1xl">
            enter the two people who have sacrificed themselves to exorcise the
            witch
          </p>
          <div class="divider my-0" />
          {#if !statusAlert} 
          <div class="modal-action">
            <button
              class={dataperson.length == 2
                ? `btn btn-success text-white`
                : `btn text-white`}
              on:click={() => {
                if (dataperson.length == 2) {
                  getAverage();
                } else {
                  $form = {
                    personName: "",
                    ageOfDeath: 0,
                    yearOfdeath: 0,
                  };
                  $errors["personName"] = "";
                  $errors["ageOfDeath"] = "";
                  $errors["yearOfdeath"] = "";

                  handleClick();
                }
              }}>{dataperson.length == 2 ? "execute" : "enter"}</button
            >
          </div>
          {/if}

          <div>
            <div class="grid grid-cols-2 gap-2 mt-2">
              {#each dataperson as { personName, ageOfDeath, yearOfdeath, bornOnYear, peopleKilled }, i}
                <div class="card w-72 bg-base-100 shadow-xl">
                  <div class="card-body">
                    <h2 class="card-title">{personName}</h2>
                    {#if !statusAlert} 
                    <p class="text-sm">Age Of Death {ageOfDeath}</p>
                    <p class="text-sm">Year Of Death {yearOfdeath}</p>
                    <div class="card-actions justify-end">
                      {#if processing}
                        <button class="btn btn-primary loading text-white"
                          >Exorcising...</button
                        >
                      {:else}
                        <button
                          class="btn btn-warning text-white"
                          on:click={() => {
                            dataperson.splice(i, 1);
                            dataperson = dataperson;
                          }}>Delete</button
                        >
                      {/if}
                    </div>
                    {:else}
                    <p class="text-sm">Born On Year {bornOnYear}</p>
                    <p class="text-sm">number people killed {peopleKilled}</p>
                    {/if}
                    
                  
                  </div>
                </div>
              {/each}
            </div>
          </div>
          {#if statusAlert}
            {#if statusError}
              <div class="alert alert-error shadow-lg mt-2">
                <div>
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="stroke-current flex-shrink-0 h-6 w-6"
                    fill="none"
                    viewBox="0 0 24 24"
                    ><path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
                    /></svg
                  >
                  <span>Error! Task failed successfully. the witch has run away</span>
                </div>
                <div class="flex-none">
                  <button class="btn btn-sm" on:click="{()=>{
                   
                  }}">Again</button>
                </div>
              </div>
            {:else}
              <div class="alert alert-success shadow-lg mt-2">
                <div>
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    class="stroke-current flex-shrink-0 h-6 w-6"
                    fill="none"
                    viewBox="0 0 24 24"
                    ><path
                      stroke-linecap="round"
                      stroke-linejoin="round"
                      stroke-width="2"
                      d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
                    /></svg
                  >
                  <span>
                    <h3>Congratulations...The witch has been expelled,</h3>
                    <div class="text-sm">average number of
                      people the witch killed on year of birth
                      <div class="text-3xl font-bold">{average}</div>
                    </div>
                     </span>
                </div>
                <div class="flex-none">
                  <button class="btn btn-sm" on:click="{()=>{
                    dataperson = [];
                    dataperson = dataperson;
                    statusAlert = false;
                  }}">Again</button>
                </div>
              </div>
            {/if}
          {/if}
        </div>
      </div>
    </div>
  </div>
  <div class="drawer-side">
    <label for="my-drawer-4" class="drawer-overlay" />
    <ul class="menu p-4 overflow-y-auto w-80 bg-base-100 text-base-content">
      <form on:submit|preventDefault={handleSubmit}>
        <div class="form-control w-full max-w-xs">
          <label for="" class="label">
            <span class="label-text">Person Name</span>
          </label>
          <input
            id="personName"
            name="personName"
            type="text"
            bind:value={$form.personName}
            on:change={handleChange}
            placeholder="Type Name here"
            class={$errors.personName
              ? `input input-error w-full max-w-xs input-sm`
              : `input input-bordered w-full max-w-xs input-sm`}
          />
          {#if $errors.personName}
            <label for="" class="label">
              <span class="label-text-alt" />
              <span class="label-text-alt text-red-500"
                >{$errors.personName}</span
              >
            </label>
          {/if}
          <label for="" class="label">
            <span class="label-text">Age Of Death</span>
          </label>
          <input
            id="ageOfDeath"
            name="ageOfDeath"
            type="number"
            bind:value={$form.ageOfDeath}
            on:change={handleChange}
            placeholder="Type Age Of Death"
            class={$errors.ageOfDeath
              ? `input input-error w-full max-w-xs input-sm`
              : `input input-bordered w-full max-w-xs input-sm`}
          />
          {#if $errors.ageOfDeath}
            <label for="" class="label">
              <span class="label-text-alt" />
              <span class="label-text-alt text-red-500"
                >{$errors.ageOfDeath}</span
              >
            </label>
          {/if}
          <label for="" class="label">
            <span class="label-text">Year Of Death</span>
          </label>
          <input
            id="yearOfdeath"
            name="yearOfdeath"
            type="number"
            bind:value={$form.yearOfdeath}
            on:change={handleChange}
            placeholder="Type Year of Death"
            class={$errors.yearOfdeath
              ? `input input-error w-full max-w-xs input-sm`
              : `input input-bordered w-full max-w-xs input-sm`}
          />
          {#if $errors.yearOfdeath}
            <label for="" class="label">
              <span class="label-text-alt" />
              <span class="label-text-alt text-red-500"
                >{$errors.yearOfdeath}</span
              >
            </label>
          {/if}
          <div class="modal-action">
            <!-- svelte-ignore empty-block -->
            <button type="submit" class={`btn btn-sm text-white`}>
              Submit
            </button>
          </div>
        </div>
      </form>
    </ul>
  </div>
</div>

<footer class="footer items-center p-4 bg-neutral text-neutral-content">
  <p>Copyright © 2022 - Udin Dev</p>
</footer>
