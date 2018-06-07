
$ npm install windows-build-tools -g

$ npm install -g gatsby-cli
$ gasby new my-blog
$ cd my-blog

$ yarn add gatsby-source-filesystem gatsby-transformer-remark
$ yarn add babel-preset-env
$ yarn add postcss-preset-env
$ yarn add webpack 
$ npm install *
$ npm audit fix *
$ rmdir node_modules *
$ gatsby develop


```gatsby-config.js
module.exports = {
  siteMetadata: {
    title: 'Gatsby Default Starter',
  },
  plugins: [
    'gatsby-plugin-react-helmet',
    `gatsby-transformer-remark`,
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `src`,
        path: `${__dirname}/src`
      }
    }
  ],
}

```pages\2017-10-31-halloween\index.md
---
path: "/happy-halloween"
date: "2017-10-31T03:15:59.165Z"
title: "Happy Halloween"
tags: ['holiday', 'fall', 'pumpkin']
excerpt: "Great Pumpkin, or Greatest Pumpkin?"
---
# ARRRROOOOOOOO
It was a clear black night, a clear white moon...
```
```pages\2017-11-01-the-day-after\index.md
```pages\2017-12-31-slacking-on-my-blog\index.md


```pages\index.js
import React from 'react'
import Link from 'gatsby-link'

const IndexPage = ({data}) => {
  const { edges: posts } = data.allMarkdownRemark
  return (
    <div>
      {posts.map(({node: post}) => {
        const { frontmatter } = post
        return (
          <div>
            <h2>
              <Link to={frontmatter.path}>
                {frontmatter.title}
              </Link>
            </h2>
            <p>{frontmatter.date}</p>
            <p>{frontmatter.excerpt}</p>
          </div>
        )
      })}
    </div>
  )
}
export const query = graphql`
 query IndexQuery {
   allMarkdownRemark {
     totalCount
     edges {
       node {
         id
         frontmatter {
           title
           date(formatString: "MMMM DD, YYYY")
           path
           tags
           excerpt
         }
       }
     }
   }
 }
`
export default IndexPage
```

```src\templates\blog-post.js
import React from 'react'
import Link from 'gatsby-link'
import Helmet from 'react-helmet'

const Template = ({data, location}) => {
  const { markdownRemark: post } = data
  const { frontmatter, html } = post

  return (
    <div>
      <Helmet title={`${title} - My Blog`} />

      <div>
        <h1>{title}</h1>
        <h3>{date}</h3>

        <div dangerouslySetInnerHTML={{__html: html}} />
      </div>
    </div>
  )
}
export const pageQuery = graphql`
 query BlogPostByPath($path: String!) {
   markdownRemark(frontmatter: { path: { eq: $path } }) {
     html 
     frontmatter {
       title
       date(formatString: "MMMM, DD, YYYY")
       path
       tags
       excerpt
     }
   }
 }
`
export default Template
```

```src\templates\blog-post.js
import React from 'react'
import Link from 'gatsby-link'
import Helmet from 'react-helmet'

const Template = ({data, location}) => {
  const { markdownRemark: post } = data
  const { frontmatter, html } = post
  const { title, date } = frontmatter

  return (
    <div>
      <Helmet title={`${frontmatter.title} - My Blog`} />

      <div>
        <h1>{title}</h1>
        <h3>{date}</h3>

        <div dangerouslySetInnerHTML={{__html: html}} />
      </div>
    </div>
  )
}
export const pageQuery = graphql`
  query BlogPostByPath($path: String!) {
    markdownRemark(frontmatter: { path: { eq: $path } }) {
      html
      frontmatter {
        title
        date(formatString: "MMMM DD, YYYY")
        path
        tags
        excerpt
      }
    }
  }
`
export default Template
```

```gatsby-site\gatsby-node.js
const path = require('path')

exports.createPages = ({ boundActionCreators, graphql }) => {
  const { createPage } = boundActionCreators
  const blogPostTemplate = path.resolve(`src/templates/blog-post.js`)

  // If you are experiencing issues with the ordering of the posts on the homepage,
  // replace the `allMarkdownRemark` line below with something like this:
  // allMarkdownRemark(sort:{fields:[frontmatter___date], order: ASC}) {

  return graphql(`{
    allMarkdownRemark {
      edges {
        node {
          html
          id
          frontmatter {
            date
            path
            title
            excerpt
            tags
          }
        }
      }
    }
  }`)
    .then(result => {
      if (result.errors) {
        return Promise.reject(result.errors)
      }

      const posts = result.data.allMarkdownRemark.edges

      posts.forEach(({node}, index) => {
        createPage({
          path: node.frontmatter.path,
          component: blogPostTemplate
        })
      })
    })
}
```