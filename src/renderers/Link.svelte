<script>
  export let href = ''
  export let title = undefined
</script>

<a {href} {title} target="_new"><slot></slot></a>
