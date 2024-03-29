<script>
  import { slide } from 'svelte/transition'
  import { branch_data } from '@/store'
  import { events } from '@/sample_data/events'
  import { re_param } from '@/utils'

  import Divider from '@/components/Divider.svelte'
  import IconCards from '@/components/home/IconCards.svelte'
  import LiteYT from '@/components/LiteYT.svelte'
  import LoadFailed from '@/components/LoadFailed.svelte'
  import Loading from '@/components/Loading.svelte'
  import NewsTicker from '@/components/NewsTicker.svelte'
  import Slider from '@/components/Slider.svelte'
  import UpcomingCard from '@/components/home/UpcomingCard.svelte'
  import VideoHeader from '@/components/home/VideoHeader.svelte'

  let currentELimit = 4
  let currentALimit = 4

  let eventRetryTrigger = 0
  let announceRetryTrigger = 0
  let upcomingRetryTrigger = 0

  // eslint-disable-next-line no-unused-vars
  const getEvents = async (category, branch, limit, trigger) => {
    const conf = { limit }
    if (category.trim() != '') conf['filter[category]'] = category
    if (branch.trim() != '') conf['filter[branch]'] = branch

    let response = await fetch(re_param('collections/get/posts?sort[_created]=-1', conf))

    let data = await response.json()
    // console.log(category, data)
    return data || events
  }

  // eslint-disable-next-line no-unused-vars
  const getUpcomingEvent = async (limit, branch, trigger) => {
    const conf = { limit }
    if (branch.trim() != '') conf['filter[branch]'] = branch

    let response = await fetch(re_param('collections/get/upcoming_events?sort[_created]=-1', conf))
    let data = await response.json()

    return data
  }

  $: a_event_data = getEvents('event', $branch_data.name.toLowerCase(), currentELimit, eventRetryTrigger)
  $: a_announce_data = getEvents('announcement', $branch_data.name.toLowerCase(), currentALimit, announceRetryTrigger)
  $: upcoming_data = getUpcomingEvent(4, $branch_data.name.toLowerCase(), upcomingRetryTrigger)
</script>

<div transition:slide class="page-home">
  <div class="hidden md:block">
    <NewsTicker />
  </div>
  <VideoHeader />
  <div class="container mx-auto px-4">
    <div class="iconcard-section flex flex-wrap justify-center children:mx-4 -mt-8 <md:(max-w-4/5 mx-auto) md:(max-w-screen-lg -mt-10 mx-auto)">
      <IconCards />
    </div>
    <Divider />
    <div class="news-section px-4">
      <div class="text-primary-900 text-2xl capitalize font-bold mb-4">latest news</div>
      {#await a_event_data}
        <Loading>Loading News</Loading>
      {:then e_data}
        <Slider datas={e_data.entries} />
      {:catch}
        <LoadFailed on:retry={() => eventRetryTrigger++} />
      {/await}
    </div>
    <Divider />
    <div class="announcement-section mb-16 px-4">
      <div class="text-primary-900 text-2xl capitalize font-bold mb-4">announcements</div>
      {#await a_announce_data}
        <Loading>Loading Announcements</Loading>
      {:then a_data}
        <Slider datas={a_data.entries} />
      {:catch}
        <LoadFailed on:retry={() => announceRetryTrigger++} />
      {/await}
    </div>
    <Divider />
    <div class="upcoming-section mb-16 px-4">
      <div class="text-primary-900 text-2xl capitalize font-bold mb-4">upcoming events</div>
      <div class="grid gap-4 grid-cols-1 lg:grid-cols-2 xl:grid-cols-3">
        {#await upcoming_data}
          <Loading>Loading Upcoming Events</Loading>
        {:then u_data}
          {#each u_data.entries as data}
            {#if data.published}
              <UpcomingCard {data} />
            {/if}
          {/each}
        {:catch}
          <LoadFailed on:retry={() => upcomingRetryTrigger++} />
        {/await}
      </div>
    </div>

    {#if $branch_data.homepage_embed}
      <Divider />
      <!-- wrappers-wrapper -->
      <div class="w-full mb-12">
        <!-- iframe-wrapper -->
        <div class="<sm:(relative overflow-hidden w-full pt-[56.25%])">
          <!-- iframe -->

          <!-- <iframe src={$branch_data.homepage_embed} title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen class="<sm:(absolute top-0 left-0 bottom-0 right-0 w-full h-full)" width="100%" height="450"></iframe> -->
          <LiteYT videoID={$branch_data.homepage_embed} class="<sm:(absolute top-0 left-0 bottom-0 right-0 w-full h-full) w-full h-450px" />
        </div>
      </div>
    {/if}
  </div>
</div>
