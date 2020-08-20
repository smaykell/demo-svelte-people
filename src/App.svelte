<script>
  import { onMount } from "svelte";
  import axios from "axios";

  const url = "http://localhost:5000";

  const fields = [
    "Id",
    "Nombre",
    "Genero",
    "Fec. Nac.",
    "Peso",
    "Altura",
    "Operación",
  ];

  const genders = [
    { value: "M", text: "Masculino" },
    { value: "F", text: "Femenino" },
  ];

  let people = [];

  let person = {
    id: null,
    name: "",
    gender: "M",
    birthDate: "",
    weight: 0,
    height: 0,
  };

  onMount(() => {
    findAll();
  });

  function clearForm() {
    person = {
      id: null,
      name: "",
      gender: "M",
      birthDate: "",
      weight: 0,
      height: 0,
    };
  }

  async function findAll() {
    try {
      const res = await axios.get(`${url}/api/people/`);
      people = res.data;
    } catch (error) {
      alert(error);
    }
  }

  async function finOne(id) {
    try {
      const res = await axios.get(`${url}/api/people/${id}`);
      person = res.data;
    } catch (error) {
      alert(error);
    }
  }

  async function createPerson() {
    try {
      const res = await axios.post(`${url}/api/people/`, person);
      person = res.data;
      findAll();
    } catch (error) {
      alert(error);
    }
  }

  async function updatePerson(id) {
    try {
      const res = await axios.put(`${url}/api/people/${id}`, person);
      person = res.data;
      findAll();
    } catch (error) {
      alert(error);
    }
  }

  async function deletePerson(id) {
    try {
      const res = await axios.delete(`${url}/api/people/${id}`);
      if (res.status === 200) findAll();
    } catch (error) {
      alert(error);
    }
  }
</script>

<main>
  <div class="container mt-4">
    <div class="row">
      <div class="col col-md-4">
        <form>
          <div class="form-row">
            <div class="form-group col-md-12">
              <label for="name">Nombre</label>
              <input
                id="name"
                class="form-control"
                type="text"
                bind:value={person.name} />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-6">
              <label for="gender">Genero</label>
              <select
                id="gender"
                class="form-control"
                bind:value={person.gender}>
                {#each genders as gender}
                  <option>{gender.text}</option>
                {/each}
              </select>
            </div>
            <div class="form-group col-md-6">
              <label for="birth-date">Fec. Nac.</label>
              <input
                id="birth-date"
                class="form-control"
                type="date"
                bind:value={person.birthDate}
                required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-6">
              <label for="weight">Peso</label>
              <input
                id="weight"
                class="form-control"
                type="text"
                bind:value={person.weight}
                required />
            </div>
            <div class="form-group col-md-6">
              <label for="height">Altura</label>
              <input
                id="height"
                class="form-control"
                type="text"
                bind:value={person.height}
                required />
            </div>
          </div>
          <div class="form-row">
            <div class="form-group col-md-6">
              {#if person.id == null}
                <button
                  class="btn btn-outline-primary btn-sm"
                  on:click|preventDefault={createPerson}>
                  Guardar
                </button>
              {:else}
                <button
                  class="btn btn-outline-warning btn-sm"
                  on:click|preventDefault={updatePerson(person.id)}>
                  Actualizar
                </button>
              {/if}
              <button
                class="btn btn-outline-secondary btn-sm"
                on:click|preventDefault={clearForm}>
                Cancelar
              </button>
            </div>
          </div>
        </form>
      </div>
      <div class="col">
        <div class="table-responsive">
          <table class="table table-hover">
            <thead>
              <tr>
                {#each fields as field}
                  <th>{field}</th>
                {/each}
              </tr>
            </thead>
            <tbody>
              {#each people as person}
                <tr>
                  <th scope="row">{person.id}</th>
                  <td>{person.name}</td>
                  <td>{person.gender}</td>
                  <td>{person.birthDate}</td>
                  <td>{person.weight}</td>
                  <td>{person.height}</td>
                  <td>
                    <button
                      class="btn btn-outline-primary btn-sm"
                      on:click={() => finOne(person.id)}>
                      Editar
                    </button>
                    <button
                      class="btn btn-outline-danger btn-sm"
                      on:click={() => deletePerson(person.id)}>
                      Eliminar
                    </button>
                  </td>
                </tr>
              {/each}
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</main>
