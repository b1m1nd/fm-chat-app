<script>
  import gsap from 'gsap'
  import User from './User.svelte'
  import Chat from './Chat.svelte'
  import { afterUpdate, onMount } from 'svelte'
  import * as msgData from '../data/messages.json'

  const data = JSON.stringify(msgData)
  let msgs = JSON.parse(data).default

  let input
  let chatBody
  let chatMessagesBody
  let selectedOption
  let hasOption = true
  let isAnimated = true

  function userInput(e) {
    if (!input.value) return

    const msg = {
      user: true,
      msg: input.value,
    }

    if (!selectedOption) {
      msg.user = false
      msg.msg = 'Please select a package'
    } else if (hasOption) {
      msg.user = false
      msg.msg = `Thank you, invoice and contract will be emailed.`
      hasOption = false
    }

    msgs = [...msgs, msg]
    input.value = ''
  }

  function confirmMsg(e) {
    if (!hasOption) return

    const value = e.detail.price
    input.value = `Confirm $${value} payment`
    selectedOption = value
  }

  onMount(() => {
    const chatMessages = chatMessagesBody.children

    gsap.from(chatMessages, {
      display: 'none',
      duration: 0.5,
      autoAlpha: 0,
      y: 20,
      stagger: 1.2,
      onComplete: () => {
        isAnimated = false
      },
    })
  })

  afterUpdate(() => {
    chatBody.scrollTo(0, 999999)
  })

  $: msgs
</script>

<div class="phone-wrap">
  <div class="phone">
    <div class="chat-header">
      <User />
    </div>

    <div class="chat-body" bind:this={chatBody}>
      <div class="chat-messages" bind:this={chatMessagesBody}>
        <b />

        {#each msgs as msg}
          <Chat
            on:confirm={confirmMsg}
            user={msg.user}
            img={msg.img}
            imgs={msg.imgs}
            option={msg.option}
            price={msg.price}
          >
            {msg.msg}
          </Chat>
        {/each}
      </div>
    </div>

    <form class="chat-input" on:submit|preventDefault={userInput}>
      <label>
        <input
          type="text"
          placeholder="type a message"
          bind:this={input}
          disabled={isAnimated}
        />
      </label>

      <b on:click={userInput}>&gt;</b>
    </form>
  </div>
</div>

<style type="text/scss">
  //style like its 1999

  .phone-wrap {
    width: min-content;
    padding: 10px;
    background-color: var(--clr-white);
    border-radius: 25px;
    box-shadow: 10px 10px 20px 1px #1b1b1b20;
  }

  .phone {
    width: 250px;
    height: 500px;
    border-radius: 20px;
    background-color: var(--clr-dark-gray);
    overflow: hidden;
  }

  .chat-header {
    position: relative;
    width: 100%;
    color: var(--clr-white);
    border-radius: 20px 20px 5px 5px;
    background: linear-gradient(-90deg, var(--clr), var(--clr-two));
    box-shadow: 0px 5px 10px 5px #1b1b1b20;

    &::before {
      position: relative;
      content: '';
      width: 125px;
      height: 20px;
      margin: 0 auto;
      display: block;
      border-radius: 0 0 10px 10px;
      background-color: var(--clr-white);
    }
  }

  .chat-body {
    position: relative;
    height: 385px;
    margin-bottom: 5px;
    overflow-y: auto;
    &::-webkit-scrollbar {
      width: 0;
      height: 0;
      -ms-overflow-style: none;
    }
  }

  .chat-messages {
    width: 100%;
    height: 100%;
    padding: 0.5rem;
    display: grid;
    grid-template-rows: 1fr;
    // - //fixme: scroll for grid end or js scrollTo?
    // align-content: end;
  }

  .chat-input {
    height: 35px;
    margin: 0 7px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    background-color: var(--clr-white);
    border-radius: 20px;

    input {
      margin-left: 10px;
      border: none;
      outline: transparent;
    }

    b {
      width: 25px;
      height: 25px;
      margin-right: 5px;
      color: var(--clr-white);
      text-align: center;
      background-color: var(--clr-two-dark);
      border: none;
      border-radius: 50%;
      cursor: pointer;
    }
  }
</style>
