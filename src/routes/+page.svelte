<script>
  import { supabase } from '$lib/supabase.js'
  import { onMount } from 'svelte'

  let name = ''
  let description = ''
  let picture = ''
  let price = ''
  let status = ''
  let isImageValid = true

  // @ts-ignore
  function isValidImageURL(url) {
    try {
      const parsed = new URL(url)
      return parsed.protocol === 'https:'
    } catch {
      return false
    }
  }

  async function addItem() {
    if (!name.trim() || !description.trim() || !picture.trim() || !price) {
      status = '❌ All fields must be filled.'
      return
    }

    if (!isValidImageURL(picture)) {
      status = '❌ Picture URL must be a valid HTTPS link.'
      return
    }

    if (!isImageValid) {
      status = '❌ The image URL is not loading correctly.'
      return
    }

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
      status = `❌ Supabase error: ${error.message}`
    } else {
      status = `✅ Item "${name}" added successfully!`
      name = ''
      description = ''
      picture = ''
      price = ''
      isImageValid = true
    }
  }

  function resetForm() {
    name = ''
    description = ''
    picture = ''
    price = ''
    status = ''
    isImageValid = true
  }

  onMount(() => {
    // @ts-ignore
    particlesJS("particles-js", {
      particles: {
        number: { value: 80, density: { enable: true, value_area: 800 } },
        color: { value: "#ffffff" },
        shape: { type: "circle", stroke: { width: 0, color: "#000000" }, polygon: { nb_sides: 5 } },
        opacity: { value: 0.5 },
        size: { value: 3, random: true },
        line_linked: { enable: true, distance: 150, color: "#ffffff", opacity: 0.4, width: 1 },
        move: { enable: true, speed: 6, out_mode: "out" }
      },
      interactivity: {
        detect_on: "canvas",
        events: {
          onhover: { enable: true, mode: "repulse" },
          onclick: { enable: true, mode: "push" },
          resize: true
        },
        modes: {
          repulse: { distance: 200, duration: 0.4 },
          push: { particles_nb: 4 }
        }
      },
      retina_detect: true
    });

    // @ts-ignore
    const stats = new Stats();
    stats.setMode(0);
    stats.domElement.style.position = 'absolute';
    stats.domElement.style.left = '0px';
    stats.domElement.style.top = '0px';
    document.body.appendChild(stats.domElement);

    const count_particles = document.querySelector('.js-count-particles');
    const update = () => {
      stats.begin();
      stats.end();
      // @ts-ignore
      const particles = window.pJSDom?.[0]?.pJS?.particles?.array;
      // @ts-ignore
      if (particles) count_particles.innerText = particles.length;
      requestAnimationFrame(update);
    };
    requestAnimationFrame(update);
  });
</script>

<svelte:head>
  <script src="https://cdn.jsdelivr.net/particles.js/2.0.0/particles.min.js"></script>
  <script src="https://threejs.org/examples/js/libs/stats.min.js"></script>
</svelte:head>

<!-- Particle background -->
<div id="particles-js"></div>

<main class="form-wrapper">
  <h1 class="form-title">ADD ITEM</h1>
  <p class="form-subtitle">Please fill out the item details below to add a new product to your list.</p>

  <form on:submit|preventDefault={addItem} class="form-box">
    <div class="form-group">
      <label for="name">Item Name</label>
      <input id="name" bind:value={name} required placeholder="e.g., Iron Sword" />
    </div>

    <div class="form-group">
      <label for="desc">Description</label>
      <input id="desc" bind:value={description} required placeholder="Short item description..." />
    </div>

    <div class="form-group">
      <label for="picture">Picture URL</label>
      <input
        id="picture"
        bind:value={picture}
        required
        placeholder="https://example.com/image.jpg"
        on:input={() => isImageValid = true}
      />
    </div>

    {#if picture}
      <div class="image-preview">
        <p>🔍 Image Preview:</p>
        <img
          src={picture}
          alt="Image preview"
          on:error={() => isImageValid = false}
          on:load={() => isImageValid = true}
        />
        {#if !isImageValid}
          <p class="image-error">❌ This URL does not point to a valid image.</p>
        {/if}
      </div>
    {/if}

    <div class="form-group">
      <label for="price">Price</label>
      <input id="price" type="number" bind:value={price} min="1" required placeholder="Enter item price" />
    </div>

    <div class="form-buttons">
      <button type="submit" class="submit">SUBMIT</button>
      <button type="button" on:click={resetForm} class="reset">RESET</button>
    </div>

    {#if status}
      <p class="status">{status}</p>
    {/if}
  </form>
</main>

<style>
  #particles-js {
    position: fixed;
    width: 100%;
    height: 100%;
    background: #000;
    top: 0;
    left: 0;
    z-index: -1;
  }

  .count-particles {
    position: absolute;
    top: 50px;
    left: 10px;
    color: #13E8E9;
    font-size: 0.8rem;
    z-index: 10;
    background: #000022;
    padding: 4px;
    border-radius: 0 0 4px 0;
    font-family: Helvetica, Arial, sans-serif;
  }

  .form-wrapper {
    max-width: 600px;
    margin: 5rem auto;
    padding: 2rem;
    text-align: center;
    position: relative;
    z-index: 1;
    color: #fff;
  }

  .form-title {
    font-size: 2rem;
    letter-spacing: 2px;
    margin-bottom: 0.5rem;
  }

  .form-subtitle {
    font-size: 0.9rem;
    color: #ccc;
    margin-bottom: 2rem;
  }

  .form-box {
    background: #0f1d27;
    padding: 2rem;
    border: 2px solid #aaa;
    border-radius: 6px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.5);
    text-align: left;
  }

  .form-group {
    margin-bottom: 1.5rem;
  }

  label {
    display: block;
    margin-bottom: 0.3rem;
    font-weight: 500;
    color: #eee;
  }

  input {
    width: 100%;
    padding: 0.6rem;
    background: #182b38;
    border: 1px solid #444;
    color: #fff;
    border-radius: 4px;
    font-size: 1rem;
    outline: none;
  }

  input::placeholder {
    color: #999;
  }

  .form-buttons {
    display: flex;
    justify-content: flex-end;
    gap: 1rem;
  }

  button {
    padding: 0.6rem 1.5rem;
    border: none;
    font-weight: bold;
    border-radius: 4px;
    font-size: 0.95rem;
    cursor: pointer;
    transition: background 0.2s ease-in-out;
  }

  .submit {
    background: #f1592a;
    color: white;
  }

  .submit:hover {
    background: #d14a1a;
  }

  .reset {
    background: #555;
    color: white;
  }

  .reset:hover {
    background: #333;
  }

  .status {
    margin-top: 1rem;
    text-align: center;
    color: #ffeb3b;
    font-weight: bold;
  }

  .image-preview {
    margin-top: 1rem;
    text-align: center;
  }

  .image-preview img {
    max-width: 100%;
    max-height: 200px;
    border: 1px solid #333;
    border-radius: 4px;
    margin-top: 0.5rem;
  }

  .image-error {
    color: #ff5555;
    font-size: 0.9rem;
    margin-top: 0.5rem;
    font-weight: bold;
  }
</style>
