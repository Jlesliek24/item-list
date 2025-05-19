<script>
  import { supabase } from '../lib/supabase.js'

  let name = ''
  let description = ''
  let picture = ''
  let price = 0
  let status = ''

  async function addItem() {
    const { error } = await supabase.from('items').insert([
      {
        name,
        description,
        picture,
        price: Number(price),
        is_archived: false
      }
    ])
    if (error) {
      status = `❌ Failed: ${error.message}`
    } else {
      status = `✅ Item "${name}" added successfully!`
      name = ''
      description = ''
      picture = ''
      price = 0
    }
  }
</script>

<main>
  <h1>Add Item</h1>
  <form on:submit|preventDefault={addItem}>
    <label>Name</label>
    <input bind:value={name} required />

    <label>Description</label>
    <input bind:value={description} />

    <label>Picture URL</label>
    <input bind:value={picture} />

    <label>Price</label>
    <input type="number" bind:value={price} min="0" required />

    <button type="submit">Submit</button>
  </form>

  {#if status}
    <p>{status}</p>
  {/if}
</main>

<style>
  main {
    max-width: 500px;
    margin: auto;
    padding: 2rem;
  }

  input {
    display: block;
    width: 100%;
    margin-bottom: 1rem;
    padding: 0.5rem;
  }

  button {
    padding: 0.5rem 1rem;
  }
</style>
