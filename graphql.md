# GraphQL API

Craft Pro provides an autogenerated [GraphQL](https://graphql.org) API for your content, which you can query to pull your content into separate applications, such as single-page apps (SPAs) or static site generators.

## Getting Started

Before you begin, make sure that you are running Craft 3.3 or later, and you’ve got the Pro edition installed.

### Create Your API Endpoint

The first step to adding a GraphQL API to your project is to set up a public endpoint for it.

To do that, create a [URL rule](routing.md#advanced-routing-with-url-rules) from `config/routes.php` that points to the `graphql/api` controller action. For example, the following URL rule would cause `/api` requests to route to the GraphQL API:

```php
return [
    'api' => 'graphql/api',
    // ...
];
```

You can verify that your endpoint is configured correctly, try sending a `{ping}` query to it.

```bash
curl -H "Content-Type: application/graphql" -d '{ping}' http://my-project.test/api
```

(Replace `http://my-project.test/api` with the actual URL to your endpoint.)

If that comes back with the following JSON response, then your GraphQL API is up and running!

```json
{"data":{"ping":"pong"}}
```

### Define Your Schemas

Once you’ve created a GraphQL API endpoint, you need to tell Craft which content should be available from it. (No content is available by default.) You do that by defining a **Schema**.

Craft has two types of schemas:

- The **Public Schema** defines which content should be available publicly.
- You can also define multiple private schemas, which each have their own secret **Access Token**.

You can manage your schemas from the Control Panel, at GraphQL → Schemas. In addition to defining the scope of each schema, you can also give them expiration dates, or disable them.

::: tip
When performing a GraphQL API request, the schema will be determined automatically based on the token that is supplied (if any). See [below](#querying-a-private-schema) to learn how to do that.
:::

## Sending API Requests

### Using GraphiQL

The easiest way to start exploring your GraphQL API is with the built-in [GraphiQL](https://github.com/graphql/graphiql) IDE, which is available in the Control Panel from GraphQL → Explore.

![The built-in GraphiQL IDE](./images/graphiql.png)

### Using Another IDE

Additional GraphQL IDEs are available as well:

* [Insomnia](https://insomnia.rest/)
* [GraphQL Playground](https://github.com/prisma/graphql-playground)
* [GraphQL Playground online](https://www.graphqlbin.com/v2/new)

::: tip
When you are initially exploring the API, make sure that [Dev Mode](config:devMode) is enabled so the IDE can be informed about the entire schema available to it.
:::

### Sending Requests Manually

The GraphQL API can be queried in three ways:

- Using a `GET` request, with the GraphQL query defined by a `query` parameter:
  ```bash
  curl \
    --data-urlencode "query={ping}" \
    http://craft32.test/api
  # or
  curl http://craft32.test/api?query=%7Bping%7D
  ```
- Using a `POST` request with an `application/json` content type, with the GraphQL query defined by a `query` key:
  ```bash
  curl \
    -H "Content-Type: application/json" \
    -d '{"query":"{ping}"}' \
    http://my-project.test/api
  ```
- Using a `POST` request with an `application/graphql` content type, with the GraphQL query defined by the raw request body:
  ```bash
  curl \
    -H "Content-Type: application/graphql" \
    -d '{ping}' \
    http://my-project.test/api
  ```

#### Specifying Variables

If you need to specify any [variables](https://graphql.org/learn/queries/#variables) along with your query, then you must send request as a `POST` request with an `application/json` content type, and include a `variables` key in the JSON body alongside `query`.

```bash
curl \
  -H "Content-Type: application/json" \
  -d '{
        "query": "query($id:[Int]) { entries(id: $id) { id, title } }",
        "variables": { "id": [1, 2, 3] }
      }' \
  http://my-project.test/api
```

#### Querying a Private Schema

The Public Schema will be used by default. To query against a different [schema](#define-your-schemas), pass its Access Token using an `Authorization` header.

```bash
curl \
  -H "Authorization: Bearer xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx" \
  -H "Content-Type: application/graphql" \
  -d '{entries{id}}' \
  http://my-project.test/api
```

## Query Reference

::: tip
The actual API features will depend on what your schema allows.
:::

### The `assets` query
This query is used to query for assets.

#### The `id` argument
Narrows the query results based on the elements’ IDs.

#### The `uid` argument
Narrows the query results based on the elements’ UIDs.

#### The `status` argument
Narrows the query results based on the elements’ statuses.

#### The `archived` argument
Narrows the query results to only elements that have been archived.

#### The `trashed` argument
Narrows the query results to only elements that have been soft-deleted.

#### The `site` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `siteId` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `unique` argument
Determines whether only elements with unique IDs should be returned by the query.

#### The `enabledForSite` argument
Narrows the query results based on whether the elements are enabled in the site they’re being queried in, per the `site` argument.

#### The `title` argument
Narrows the query results based on the elements’ titles.

#### The `slug` argument
Narrows the query results based on the elements’ slugs.

#### The `uri` argument
Narrows the query results based on the elements’ URIs.

#### The `search` argument
Narrows the query results to only elements that match a search query.

#### The `ref` argument
Narrows the query results based on a reference string.

#### The `fixedOrder` argument
Causes the query results to be returned in the order specified by the `id` argument.

#### The `inReverse` argument
Causes the query results to be returned in reverse order.

#### The `dateCreated` argument
Narrows the query results based on the elements’ creation dates.

#### The `dateUpdated` argument
Narrows the query results based on the elements’ last-updated dates.

#### The `offset` argument
Sets the offset for paginated results.

#### The `limit` argument
Sets the limit for paginated results.

#### The `orderBy` argument
Sets the field the returned elements should be ordered by

#### The `volumeId` argument
Narrows the query results based on the volumes the assets belong to, per the volumes’ IDs.

#### The `volume` argument
Narrows the query results based on the volumes the assets belong to, per the volumes’ handles.

#### The `folderId` argument
Narrows the query results based on the folders the assets belong to, per the folders’ IDs.

#### The `filename` argument
Narrows the query results based on the assets’ filenames.

#### The `kind` argument
Narrows the query results based on the assets’ file kinds.

#### The `height` argument
Narrows the query results based on the assets’ image heights.

#### The `width` argument
Narrows the query results based on the assets’ image widths.

#### The `size` argument
Narrows the query results based on the assets’ file sizes (in bytes).

#### The `dateModified` argument
Narrows the query results based on the assets’ files’ last-modified dates.

#### The `includeSubfolders` argument
Broadens the query results to include assets from any of the subfolders of the folder specified by `folderId`.

### The `entries` query
This query is used to query for entries.

#### The `id` argument
Narrows the query results based on the elements’ IDs.

#### The `uid` argument
Narrows the query results based on the elements’ UIDs.

#### The `status` argument
Narrows the query results based on the elements’ statuses.

#### The `archived` argument
Narrows the query results to only elements that have been archived.

#### The `trashed` argument
Narrows the query results to only elements that have been soft-deleted.

#### The `site` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `siteId` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `unique` argument
Determines whether only elements with unique IDs should be returned by the query.

#### The `enabledForSite` argument
Narrows the query results based on whether the elements are enabled in the site they’re being queried in, per the `site` argument.

#### The `title` argument
Narrows the query results based on the elements’ titles.

#### The `slug` argument
Narrows the query results based on the elements’ slugs.

#### The `uri` argument
Narrows the query results based on the elements’ URIs.

#### The `search` argument
Narrows the query results to only elements that match a search query.

#### The `ref` argument
Narrows the query results based on a reference string.

#### The `fixedOrder` argument
Causes the query results to be returned in the order specified by the `id` argument.

#### The `inReverse` argument
Causes the query results to be returned in reverse order.

#### The `dateCreated` argument
Narrows the query results based on the elements’ creation dates.

#### The `dateUpdated` argument
Narrows the query results based on the elements’ last-updated dates.

#### The `offset` argument
Sets the offset for paginated results.

#### The `limit` argument
Sets the limit for paginated results.

#### The `orderBy` argument
Sets the field the returned elements should be ordered by

#### The `withStructure` argument
Explicitly determines whether the query should join in the structure data.

#### The `structureId` argument
Determines which structure data should be joined into the query.

#### The `level` argument
Narrows the query results based on the elements’ level within the structure.

#### The `hasDescendants` argument
Narrows the query results based on whether the elements have any descendants.

#### The `ancestorOf` argument
Narrows the query results to only elements that are ancestors of another element.

#### The `ancestorDist` argument
Narrows the query results to only elements that are up to a certain distance away from the element specified by `ancestorOf`.

#### The `descendantOf` argument
Narrows the query results to only elements that are descendants of another element.

#### The `descendantDist` argument
Narrows the query results to only elements that are up to a certain distance away from the element specified by `descendantOf`.

#### The `leaves` argument
Narrows the query results based on whether the elements are “leaves” (element with no descendants).

#### The `editable` argument
Whether to only return entries that the user has permission to edit.

#### The `section` argument
Narrows the query results based on the section handles the entries belong to.

#### The `sectionId` argument
Narrows the query results based on the sections the entries belong to, per the sections’ IDs.

#### The `type` argument
Narrows the query results based on the entries’ entry type handles.

#### The `typeId` argument
Narrows the query results based on the entries’ entry types, per the types’ IDs.

#### The `authorId` argument
Narrows the query results based on the entries’ authors.

#### The `authorGroup` argument
Narrows the query results based on the user group the entries’ authors belong to.

#### The `postDate` argument
Narrows the query results based on the entries’ post dates.

#### The `before` argument
Narrows the query results to only entries that were posted before a certain date.

#### The `after` argument
Narrows the query results to only entries that were posted on or after a certain date.

#### The `expiryDate` argument
Narrows the query results based on the entries’ expiry dates.

### The `globalSets` query
This query is used to query for global sets.

#### The `id` argument
Narrows the query results based on the elements’ IDs.

#### The `uid` argument
Narrows the query results based on the elements’ UIDs.

#### The `status` argument
Narrows the query results based on the elements’ statuses.

#### The `archived` argument
Narrows the query results to only elements that have been archived.

#### The `trashed` argument
Narrows the query results to only elements that have been soft-deleted.

#### The `site` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `siteId` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `unique` argument
Determines whether only elements with unique IDs should be returned by the query.

#### The `enabledForSite` argument
Narrows the query results based on whether the elements are enabled in the site they’re being queried in, per the `site` argument.

#### The `title` argument
Narrows the query results based on the elements’ titles.

#### The `slug` argument
Narrows the query results based on the elements’ slugs.

#### The `uri` argument
Narrows the query results based on the elements’ URIs.

#### The `search` argument
Narrows the query results to only elements that match a search query.

#### The `ref` argument
Narrows the query results based on a reference string.

#### The `fixedOrder` argument
Causes the query results to be returned in the order specified by the `id` argument.

#### The `inReverse` argument
Causes the query results to be returned in reverse order.

#### The `dateCreated` argument
Narrows the query results based on the elements’ creation dates.

#### The `dateUpdated` argument
Narrows the query results based on the elements’ last-updated dates.

#### The `offset` argument
Sets the offset for paginated results.

#### The `limit` argument
Sets the limit for paginated results.

#### The `orderBy` argument
Sets the field the returned elements should be ordered by

#### The `handle` argument
Narrows the query results based on the global sets’ handles.

### The `users` query
This query is used to query for users.

#### The `id` argument
Narrows the query results based on the elements’ IDs.

#### The `uid` argument
Narrows the query results based on the elements’ UIDs.

#### The `status` argument
Narrows the query results based on the elements’ statuses.

#### The `archived` argument
Narrows the query results to only elements that have been archived.

#### The `trashed` argument
Narrows the query results to only elements that have been soft-deleted.

#### The `site` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `siteId` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `unique` argument
Determines whether only elements with unique IDs should be returned by the query.

#### The `enabledForSite` argument
Narrows the query results based on whether the elements are enabled in the site they’re being queried in, per the `site` argument.

#### The `title` argument
Narrows the query results based on the elements’ titles.

#### The `slug` argument
Narrows the query results based on the elements’ slugs.

#### The `uri` argument
Narrows the query results based on the elements’ URIs.

#### The `search` argument
Narrows the query results to only elements that match a search query.

#### The `ref` argument
Narrows the query results based on a reference string.

#### The `fixedOrder` argument
Causes the query results to be returned in the order specified by the `id` argument.

#### The `inReverse` argument
Causes the query results to be returned in reverse order.

#### The `dateCreated` argument
Narrows the query results based on the elements’ creation dates.

#### The `dateUpdated` argument
Narrows the query results based on the elements’ last-updated dates.

#### The `offset` argument
Sets the offset for paginated results.

#### The `limit` argument
Sets the limit for paginated results.

#### The `orderBy` argument
Sets the field the returned elements should be ordered by

#### The `email` argument
Narrows the query results based on the users’ email addresses.

#### The `username` argument
Narrows the query results based on the users’ usernames.

#### The `firstName` argument
Narrows the query results based on the users’ first names.

#### The `lastName` argument
Narrows the query results based on the users’ last names.

### The `tags` query
This query is used to query for tags.

#### The `id` argument
Narrows the query results based on the elements’ IDs.

#### The `uid` argument
Narrows the query results based on the elements’ UIDs.

#### The `status` argument
Narrows the query results based on the elements’ statuses.

#### The `archived` argument
Narrows the query results to only elements that have been archived.

#### The `trashed` argument
Narrows the query results to only elements that have been soft-deleted.

#### The `site` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `siteId` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `unique` argument
Determines whether only elements with unique IDs should be returned by the query.

#### The `enabledForSite` argument
Narrows the query results based on whether the elements are enabled in the site they’re being queried in, per the `site` argument.

#### The `title` argument
Narrows the query results based on the elements’ titles.

#### The `slug` argument
Narrows the query results based on the elements’ slugs.

#### The `uri` argument
Narrows the query results based on the elements’ URIs.

#### The `search` argument
Narrows the query results to only elements that match a search query.

#### The `ref` argument
Narrows the query results based on a reference string.

#### The `fixedOrder` argument
Causes the query results to be returned in the order specified by the `id` argument.

#### The `inReverse` argument
Causes the query results to be returned in reverse order.

#### The `dateCreated` argument
Narrows the query results based on the elements’ creation dates.

#### The `dateUpdated` argument
Narrows the query results based on the elements’ last-updated dates.

#### The `offset` argument
Sets the offset for paginated results.

#### The `limit` argument
Sets the limit for paginated results.

#### The `orderBy` argument
Sets the field the returned elements should be ordered by

#### The `group` argument
Narrows the query results based on the tag groups the tags belong to per the group’s handles.

#### The `groupId` argument
Narrows the query results based on the tag groups the tags belong to, per the groups’ IDs.

### The `categories` query
This query is used to query for categories.

#### The `id` argument
Narrows the query results based on the elements’ IDs.

#### The `uid` argument
Narrows the query results based on the elements’ UIDs.

#### The `status` argument
Narrows the query results based on the elements’ statuses.

#### The `archived` argument
Narrows the query results to only elements that have been archived.

#### The `trashed` argument
Narrows the query results to only elements that have been soft-deleted.

#### The `site` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `siteId` argument
Determines which site(s) the elements should be queried in. Defaults to the primary site.

#### The `unique` argument
Determines whether only elements with unique IDs should be returned by the query.

#### The `enabledForSite` argument
Narrows the query results based on whether the elements are enabled in the site they’re being queried in, per the `site` argument.

#### The `title` argument
Narrows the query results based on the elements’ titles.

#### The `slug` argument
Narrows the query results based on the elements’ slugs.

#### The `uri` argument
Narrows the query results based on the elements’ URIs.

#### The `search` argument
Narrows the query results to only elements that match a search query.

#### The `ref` argument
Narrows the query results based on a reference string.

#### The `fixedOrder` argument
Causes the query results to be returned in the order specified by the `id` argument.

#### The `inReverse` argument
Causes the query results to be returned in reverse order.

#### The `dateCreated` argument
Narrows the query results based on the elements’ creation dates.

#### The `dateUpdated` argument
Narrows the query results based on the elements’ last-updated dates.

#### The `offset` argument
Sets the offset for paginated results.

#### The `limit` argument
Sets the limit for paginated results.

#### The `orderBy` argument
Sets the field the returned elements should be ordered by

#### The `withStructure` argument
Explicitly determines whether the query should join in the structure data.

#### The `structureId` argument
Determines which structure data should be joined into the query.

#### The `level` argument
Narrows the query results based on the elements’ level within the structure.

#### The `hasDescendants` argument
Narrows the query results based on whether the elements have any descendants.

#### The `ancestorOf` argument
Narrows the query results to only elements that are ancestors of another element.

#### The `ancestorDist` argument
Narrows the query results to only elements that are up to a certain distance away from the element specified by `ancestorOf`.

#### The `descendantOf` argument
Narrows the query results to only elements that are descendants of another element.

#### The `descendantDist` argument
Narrows the query results to only elements that are up to a certain distance away from the element specified by `descendantOf`.

#### The `leaves` argument
Narrows the query results based on whether the elements are “leaves” (element with no descendants).

#### The `editable` argument
Whether to only return categories that the user has permission to edit.

#### The `group` argument
Narrows the query results based on the category groups the categories belong to per the group’s handles.

#### The `groupId` argument
Narrows the query results based on the category groups the categories belong to, per the groups’ IDs.

## List of available directives
Directives are not regulated by permissions and they affect how the returned data is displayed.

### The `formatDateTime` directive
This directive allows for formatting any date to the desired format. It can be applied to all fields, but changes anything only when applied to a DateTime field.

#### The `format` argument
This specifies the format to use. It defaults to the [Atom date time format](https://www.php.net/manual/en/class.datetimeinterface.php#datetime.constants.atom]).

#### The `timezone` argument
The full name of the timezone, defaults to UTC. (E.g., America/New_York)

### The `transform` directive
This directive is used to return a URL for an [asset tranform](https://docs.craftcms.com/v3/image-transforms.html). It accepts the same arguments you would use for a transform in Craft and adds the `immediately` argument.

#### The `handle` argument
The handle of the named transform to use.

#### The `width` argument
Width for the generated transform

#### The `height` argument
Height for the generated transform

#### The `mode` argument
The mode to use for the generated transform.

#### The `position` argument
The position to use when cropping, if no focal point specified.

#### The `interlace` argument
The interlace mode to use for the transform

#### The `quality` argument
The quality of the transform

#### The `format` argument
The format to use for the transform

#### The `immediately` argument
Whether the transform should be generated immediately or only when the image is requested used the generated URL

### The `markdown` directive
Parses the passed field value as Markdown.

#### The `flavor` argument
The “flavor” of Markdown the input should be interpreted with. Accepts the same arguments as yii\helpers\Markdown::process().

## Pre-defined interfaces
Craft defines several interfaces to be implemented by the different GraphQL types.

### The `AssetInterface` interface
This is the interface implemented by all assets.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `volumeId` field
The ID of the volume that the asset belongs to.

#### The `folderId` field
The ID of the folder that the asset belongs to.

#### The `filename` field
The filename of the asset file.

#### The `extension` field
The file extension for the asset file.

#### The `hasFocalPoint` field
Whether a user-defined focal point is set on the asset.

#### The `focalPoint` field
The focal point represented as an array with `x` and `y` keys, or null if it's not an image.

#### The `kind` field
The file kind.

#### The `size` field
The file size in bytes.

#### The `height` field
The height in pixels or null if it's not an image.

#### The `width` field
The width in pixels or null if it's not an image.

#### The `img` field
An `<img>` tag based on this asset.

#### The `url` field
The full URL of the asset. This field accepts the same fields as the `transform` directive.

#### The `mimeType` field
The file’s MIME type, if it can be determined.

#### The `path` field
The asset's path in the volume.

#### The `dateModified` field
The date the asset file was last modified.

### The `EntryInterface` interface
This is the interface implemented by all entries.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `lft` field
The element’s left position within its structure.

#### The `rgt` field
The element’s right position within its structure.

#### The `level` field
The element’s level within its structure

#### The `root` field
The element’s structure’s root ID

#### The `structureId` field
The element’s structure ID.

#### The `sectionId` field
The ID of the section that contains the entry.

#### The `sectionHandle` field
The handle of the section that contains the entry.

#### The `typeId` field
The ID of the entry type that contains the entry.

#### The `typeHandle` field
The handle of the entry type that contains the entry.

#### The `authorId` field
The ID of the author of this entry.

#### The `author` field
The entry's author.

#### The `postDate` field
The entry's post date.

#### The `expiryDate` field
The expiry date of the entry.

#### The `children` field
The entry’s children, if the section is a structure.  Accepts the same arguments as the `entries` query.

#### The `parent` field
The entry’s parent, if the section is a structure.

### The `GlobalSetInterface` interface
This is the interface implemented by all global sets.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `name` field
The name of the global set.

#### The `handle` field
The handle of the global set.

### The `MatrixBlockInterface` interface
This is the interface implemented by all matrix blocks.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `fieldId` field
The ID of the field that owns the matrix block.

#### The `ownerId` field
The ID of the element that owns the matrix block.

#### The `typeId` field
The ID of the matrix block's type.

#### The `typeHandle` field
The handle of the matrix block's type.

#### The `sortOrder` field
The sort order of the matrix block within the owner element field.

### The `UserInterface` interface
This is the interface implemented by all users.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `friendlyName` field
The user's first name or username.

#### The `fullName` field
The user's full name.

#### The `name` field
The user's full name or username.

#### The `photo` field
The user's photo.

#### The `preferences` field
The user’s preferences.

#### The `preferredLanguage` field
The user’s preferred language.

#### The `username` field
The username.

#### The `firstName` field
The user's first name.

#### The `lastName` field
The user's last name.

#### The `email` field
The user's email.

### The `CategoryInterface` interface
This is the interface implemented by all categories.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `lft` field
The element’s left position within its structure.

#### The `rgt` field
The element’s right position within its structure.

#### The `level` field
The element’s level within its structure

#### The `root` field
The element’s structure’s root ID

#### The `structureId` field
The element’s structure ID.

#### The `groupId` field
The ID of the group that contains the category.

#### The `groupHandle` field
The handle of the group that contains the category.

#### The `children` field
The category’s children.

#### The `parent` field
The category’s parent.

### The `TagInterface` interface
This is the interface implemented by all tags.

#### The `id` field
The id of the entity

#### The `uid` field
The uid of the entity

#### The `title` field
The element’s title.

#### The `slug` field
The element’s slug.

#### The `uri` field
The element’s URI.

#### The `enabled` field
Whether the element is enabled or not.

#### The `archived` field
Whether the element is archived or not.

#### The `siteId` field
The ID of the site the element is associated with.

#### The `searchScore` field
The element’s search score, if the `search` parameter was used when querying for the element.

#### The `trashed` field
Whether the element has been soft-deleted or not.

#### The `status` field
The element's status.

#### The `dateCreated` field
The date the element was created.

#### The `dateUpdated` field
The date the element was last updated.

#### The `groupId` field
The ID of the group that contains the tag.

#### The `groupHandle` field
The handle of the group that contains the tag.

## Interface Implementation

A defined type exists for each specific interface implementation. For example, if a “News” section has “Article” and “Editorial” entry types, in addition to the `EntryInterface` interface type, two additional types would be defined the GraphQL schema, if the token used allows it: `news_article_Entry` and `news_editorial_Entry` types.

## An example query and response

### Query payload

```graphql
{
  queryEntries (section: "news", limit: 2, orderBy: "dateCreated DESC"){
    dateCreated @formatDateTime (format: "Y-m-d")
    title
    children {
      title
    }
    ... on news_article_Entry {
      shortDescription
      featuredImage {
        url @transform (width: 300, immediately: true)
      }
    }
  }
}
```

### The response

```json
{
  "data": {
    "queryEntries": [
      {
        "dateCreated": "2019-08-21",
        "title": "An important news item",
        "children": [],
        "shortDescription": "<p>This is how we roll these days.</p>",
        "featuredImage": [
          {
            "url": "/assets/site/_300xAUTO_crop_center-center_none/glasses.jpg"
          }
        ]
      },
      {
        "dateCreated": "2019-07-02",
        "title": "Dolorem ea eveniet alias",
        "children": [
          {
            "title": "Child entry"
          },
          {
            "title": "This is also a child entry"
          }
        ]
        "shortDescription": "Et omnis explicabo iusto eum nobis. Consequatur debitis architecto est exercitationem vitae velit repellendus. Aut consequatur maiores error ducimus ea et. Rem ipsa asperiores eius quas et omnis. Veniam quasi qui repellendus dignissimos et necessitatibus. Aut a illo tempora.",
        "featuredImage": []
      }
    ]
  }
}
```