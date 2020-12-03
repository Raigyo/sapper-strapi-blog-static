<!-- This file represents our /articles route. -->

<script context="module">
  import ApolloClient, { gql } from 'apollo-boost'; // Apollo Client to make a query: blogQuery.
  import moment from 'moment';

  const blogQuery = gql`
  query Blogs {
          blogs {
                  id
                  Title
                  Description
                  Published
                  Body
                  author {
                          username
                  }
                  Slug
          }
  }
  `;

  // preload() function to process the data needed on our page. The function then returns posts,
  // a variable with the parsed query result.
  export async function preload({params, query}) {
          const client = new ApolloClient({
                  // uri: '<Your Strapi GraphQL Endpoint>',
                  uri: 'http://localhost:1337/graphql',
                  fetch: this.fetch
                   });
          // we store our query response in a variable
          const results = await client.query({
                  query: blogQuery
          })
          return {posts: results.data.blogs}
  }
</script>

<script>
  export let posts;
</script>

<svelte:head>
  <title>articles</title>
</svelte:head>

<h1>recent posts</h1>

<!--
  We’ve used Svelte’s #each block to loop through the data from Strapi, displaying the title,
  date of publication, and author. Our <a> tag, when clicked, goes to a page defined
  by the slug that we entered for our post in Strapi’s admin UI.
  This means that when the link is clicked, we open up a page for a particular article,
  and the slug is used to identify that article.
-->
<ul>
{#each posts as post}
<li>
    <a class="main-title" rel='prefetch' href='articles/{post.Slug}'>
      {post.Title}
    </a>
</li>
<p>
{moment().to(post.Published, "DD-MM-YYYY")} ago by {post.author.username}
</p>
{/each}
</ul>

<style>
  ul, p {
          margin: 0 0 1em 0;
          line-height: 1.5;
  }
  .main-title {
          font-size: 25px;
  }
</style>
