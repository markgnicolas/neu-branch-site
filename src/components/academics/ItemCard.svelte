<script>
  import SkeletonImage from '../SkeletonImage.svelte'

  export let data = {
    img: './images/acad/A.png',
    text: 'Undergraduate',
    showMore: true
  }
</script>

<div on:click class="cursor-pointer bg-light-800 rounded relative overflow-hidden group transform transform-scale transition transition-scale scale-100 hover:scale-102 hover:shadow-lg hover:bg-light-50">
  <SkeletonImage class='w-full relative overflow-hidden' style='aspect-ratio: 1/1;' img={data.img} viewable={false}/>
  <div class="p-2 flex flex-col justify-between">
    <div class="text-center font-light text-2xl text-primary-900 truncate">
      {data.text}
    </div>
    {#if data.showMore}
      <div class="flex justify-center mt-4">
        <div class="text-center text-gray-800 text-xl mb-4 group-hover:text-primary-500 border-b-2 border-transparent group-hover:border-b-primary-900">
          More
        </div>
      </div>
    {/if}
  </div>
</div>