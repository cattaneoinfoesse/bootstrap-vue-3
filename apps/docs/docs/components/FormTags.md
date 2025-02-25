# Form Tags

Lightweight custom tagged input form control, with options for customized interface rendering, duplicate tag detection and optional tag validation.

Tags are arrays of short strings, used in various ways such as assigning categories. Use the default user interface, or create your own custom interface via the use of the default scoped slot.

## Basic usage

Tags will have any leading and tailing whitespace removed, and duplicate tags are not permitted. Tags that contain spaces are permitted by default.

Tags are added by clicking the **Add** button, pressing the Enter key or optionally when the `change` event fires on the new tag input (i.e. when focus moves from the input). The **Add** button will only appear when the user has entered a new tag value.

**Default render:**

<ClientOnly>
  <label for="tags-basic">Type a new tag and press enter</label>
  <b-form-tags input-id="tags-basic" v-model="value"></b-form-tags>
  <p class="mt-2">Value: {{ value }}</p>
</ClientOnly>

```html
<template>
  <label for="tags-basic">Type a new tag and press enter</label>
  <b-form-tags input-id="tags-basic" v-model="value"></b-form-tags>
  <p class="mt-2">Value: {{ value }}</p>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const value = ref<string[]>(['apple', 'orange'])
</script>
```

You can disable adding a new tag when pressing <kbd>Enter</kbd> via the `no-add-on-enter` prop, and enable adding a tag on the input's `change` event via the `add-on-change` prop.

## Tag creation using separators

To auto create tags when a separator character is typed (i.e. <kbd>Space</kbd>, <kbd>,</kbd>, etc.), set the `separator` prop to the character that will trigger the tag to be added. If multiple separator characters are needed, then include them as a single string (i.e. `' ,;'`), or an array of characters (i.e. `[' ', ',', ';']`), which will trigger a new tag to be added when <kbd>Space</kbd>, <kbd>,</kbd>, or <kbd>;</kbd> are typed). Separators must be a single character.

The following example will auto create a tag when <kbd>Space</kbd>, <kbd>,</kbd>, or <kbd>;</kbd> are typed:

<ClientOnly>
  <label for="tags-separators">Enter tags separated by space, comma or semicolon</label>
  <b-form-tags
    input-id="tags-separators"
    v-model="value2"
    separator=" ,;"
    placeholder="Enter new tags separated by space, comma or semicolon"
    no-add-on-enter
  ></b-form-tags>
  <p class="mt-2">Value: {{ value2 }}</p>
</ClientOnly>

```html
<template>
  <label for="tags-separators">Enter tags separated by space, comma or semicolon</label>
  <b-form-tags
    input-id="tags-separators"
    v-model="value"
    separator=" ,;"
    placeholder="Enter new tags separated by space, comma or semicolon"
    no-add-on-enter
  ></b-form-tags>
  <p class="mt-2">Value: {{ value }}</p>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const value = ref<string[]>(['one', 'two'])
</script>
```

## Last tag removal via backspace keypress

When the prop `remove-on-delete` is set, and the user presses <kbd>Backspace</kbd> (or <kbd>Del</kbd>) and the input value is empty, the last tag in the tag list will be removed.

<ClientOnly>
  <label for="tags-remove-on-delete">Enter new tags separated by space</label>
  <b-form-tags
    input-id="tags-remove-on-delete"
    :input-attrs="{ 'aria-describedby': 'tags-remove-on-delete-help' }"
    v-model="value3"
    separator=" "
    placeholder="Enter new tags separated by space"
    remove-on-delete
    no-add-on-enter
  ></b-form-tags>
  <b-form-text id="tags-remove-on-delete-help" class="mt-2">
    Press <kbd>Backspace</kbd> to remove the last tag entered
  </b-form-text>
  <p>Value: {{ value3 }}</p>
</ClientOnly>

```html
<template>
  <label for="tags-remove-on-delete">Enter new tags separated by space</label>
  <b-form-tags
    input-id="tags-remove-on-delete"
    :input-attrs="{'aria-describedby': 'tags-remove-on-delete-help'}"
    v-model="value"
    separator=" "
    placeholder="Enter new tags separated by space"
    remove-on-delete
    no-add-on-enter
  ></b-form-tags>
  <b-form-text id="tags-remove-on-delete-help" class="mt-2">
    Press <kbd>Backspace</kbd> to remove the last tag entered
  </b-form-text>
  <p>Value: {{ value }}</p>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const value = ref<string[]>(['apple', 'orange', 'grape'])
</script>
```

## Styling Options

Several props are available to alter the basic styling of the default tagged interface:

| Prop          | Description                                                                                           |
| ------------- | ----------------------------------------------------------------------------------------------------- |
| `tag-pills`   | Renders the tags with the appearance of pills                                                         |
| `tag-variant` | Applies one of the Bootstrap contextual variant theme colors to the tags                              |
| `size`        | Set the size of the component's appearance. 'sm', 'md' (default), or 'lg'                             |
| `placeholder` | The placeholder text for the new tag input element                                                    |
| `state`       | Sets the contextual state of the control. Set to `true` (for valid), `false` (for invalid), or `null` |
| `disabled`    | Places the component in a disabled state                                                              |

For additional props, see the component reference section at the bottom of this page.

The focus and validation state styling of the component relies upon BootstrapVue's custom CSS.

## Using with native browser `<form>` submission

The value of the tagged input will not be submitted via standard form `action` unless you provide a `name` via the name prop. When a name is provided, `<b-form-tags>` will create a hidden `<input>` for each tag. The hidden input will have the name attribute set to the value of the name prop.

The hidden inputs will also be generated when using [custom rendering](#custom-rendering-with-default-scoped-slot) (when the `name` prop is set).

## Tag validation

By default, `<b-form-tags>` detects when the user is attempting to enter a (case-sensitive) duplicate tag, and will provide integrated feedback to the user.

You can optionally provide a tag validator method via the `tag-validator` prop. The validator function will receive one argument which is the tag being added, and should return either `true` if the tag passes validation and can be added, or `false` if the tag fails validation (in which case it is not added to the array of tags). Integrated feedback will be provided to the user listing the invalid tag(s) that could not be added.

Tag validation occurs only for tags added via user input. Changes to the tags via the `v-model` are not validated.

<ClientOnly>
  <b-form-group label="Tags validation example" label-for="tags-validation" :state="state">
    <b-form-tags
      input-id="tags-validation"
      v-model="tags"
      :input-attrs="{ 'aria-describedby': 'tags-validation-help' }"
      :tag-validator="tagValidator"
      :state="state"
      separator=" "
    ></b-form-tags>
  
  <template #invalid-feedback>
    You must provide at least 3 tags and no more than 8
  </template>

<template #description>

<div id="tags-validation-help">
Tags must be 3 to 5 characters in length and all lower
case. Enter tags separated by spaces or press enter.
</div>
</template>
</b-form-group>
</ClientOnly>

```html
<template>
  <b-form-group label="Tags validation example" label-for="tags-validation" :state="state">
    <b-form-tags
      input-id="tags-validation"
      v-model="tags"
      :input-attrs="{'aria-describedby': 'tags-validation-help'}"
      :tag-validator="tagValidator"
      :state="state"
      separator=" "
    ></b-form-tags>

    <template #invalid-feedback> You must provide at least 3 tags and no more than 8 </template>

    <template #description>
      <div id="tags-validation-help">
        Tags must be 3 to 5 characters in length and all lower case. Enter tags separated by spaces
        or press enter.
      </div>
    </template>
  </b-form-group>
</template>

<script setup lang="ts">
  import {ref, computed, watch} from 'vue'

  const tags = ref<string[]>([])
  const dirty = ref(false)

  const state = computed(() =>
    dirty.value ? tags.value.length > 2 && tags.value.length < 9 : null
  )

  watch(tags, () => (dirty.value = false))

  function tagValidator(tag) {
    return tag === tag.toLowerCase() && tag.length > 2 && tag.length < 6
  }
</script>
```

## Detecting new, invalid, and duplicate tags

The event `tag-state` will be emitted whenever new tags are entered into the new tag input element, tags that do not pass validation, or duplicate tags are detected. The event handler will receive three arrays as its arguments:

- `validTags` (tags that pass validation)
- `invalidTags` (tags that do not pass validation)
- `duplicateTags` (tags that would be a duplicate of existing or validTags).

The event will be emitted only when the new tag input changes (characters are entered that would be considered part of a tag), or when the user attempts to add a tag (i.e. via <kbd>Enter</kbd>, clicking the **Add** button, or entering a separator). The three arrays will be empty when the user clears the new tag input element (or contains just spaces).

If you are providing your own feedback for duplicate and invalid tags (via the use of the `tag-state` event) outside of the `<b-form-tags>` component, you can disable the built in duplicate and invalid messages by setting the props `duplicate-tag-text` and `invalid-tag-text` (respectively) to either an empty string (`''`) or `null`.

<ClientOnly>
  <label for="tags-state-event">Enter tags</label>
  <b-form-tags
  input-id="tags-state-event"
  v-model="tags5"
  :tag-validator="validator"
  placeholder="Enter tags (3-5 characters) separated by space"
  separator=" "
  @tag-state="onTagState"
  > </b-form-tags>
  
  <p class="mt-2">Tags: {{ tags5 }}</p>
  <p>Event values:</p>
  <ul>
      <li>validTags: {{ validTags }}</li>
      <li>invalidTags: {{ invalidTags }}</li>
      <li>duplicateTags: {{ duplicateTags }}</li>
  </ul>
</ClientOnly>

```html
<template>
  <label for="tags-state-event">Enter tags</label>
  <b-form-tags
    input-id="tags-state-event"
    v-model="tags"
    :tag-validator="validator"
    placeholder="Enter tags (3-5 characters) separated by space"
    separator=" "
    @tag-state="onTagState"
  >
  </b-form-tags>

  <p class="mt-2">Tags: {{ tags }}</p>
  <p>Event values:</p>
  <ul>
    <li>validTags: {{ validTags }}</li>
    <li>invalidTags: {{ invalidTags }}</li>
    <li>duplicateTags: {{ duplicateTags }}</li>
  </ul>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const tags = ref<string[]>([])
  const validTags = ref([])
  const invalidTags = ref([])
  const duplicateTags = ref([])

  function onTagState(valid, invalid, duplicate) {
    validTags.value = valid
    invalidTags.value = invalid
    duplicateTags.value = duplicate
  }

  function validator(tag) {
    return tag.length > 2 && tag.length < 6
  }
</script>
```

## Limiting tags

If you want to limit the amount of tags the user is able to add use the `limit` prop. When configured, adding more tags than the `limit` allows is only possible by the `v-model`.

When the limit of tags is reached, the user is still able to type but adding more tags is disabled. A message is shown to give the user feedback about the reached limit. This message can be configured by the `limit-tags-text` prop. Setting it to either an empty string (`''`) or `null` will disable the feedback.

Removing tags is unaffected by the `limit` prop.

<ClientOnly>
  <label for="tags-limit">Enter tags</label>
  <b-form-tags input-id="tags-limit" v-model="value6" :limit="limit" remove-on-delete></b-form-tags>
  <p class="mt-2">Value: {{ value6 }}</p>
</ClientOnly>

```html
<template>
  <label for="tags-limit">Enter tags</label>
  <b-form-tags input-id="tags-limit" v-model="value" :limit="limit" remove-on-delete></b-form-tags>
  <p class="mt-2">Value: {{ value }}</p>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const value = ref<string[]>([])
  const limit = ref(5)
</script>
```

## Custom rendering with default scoped slot

If you fancy a different look and feel for the tags control, you can provide your own custom rendering via the default scoped slot. You can either create your own tags, or use our helper `<b-form-tag>` component.

## Scope properties

The default scoped slot provides numerous properties and methods for use in rendering your custom interface. Not all properties or methods are required to generate your interface.

The default slot scope properties are as follows:

| Property           | Type                     | Description                                                                                                                                            |
| ------------------ | ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `addButtonText`    | String                   | The value of the `add-button-text` prop                                                                                                                |
| `addButtonVariant` | String                   | The value of the `add-button-variant` prop                                                                                                             |
| `addTag`           | Function                 | Method to add a new tag. Assumes the tag is the value of the input, but optionally accepts one argument which is the tag value to be added             |
| `disableAddButton` | Boolean                  | Will be `true` if the tag(s) in the input cannot be added (all invalid and/or duplicates)                                                              |
| `disabled`         | Boolean                  | `true` if the component is in the disabled state. Value of the `disabled` prop                                                                         |
| `duplicateTagText` | String                   | The value of the `duplicate-tag-text` prop                                                                                                             |
| `duplicateTags`    | Array                    | Array of the duplicate tag(s) the user has entered                                                                                                     |
| `form`             | String                   | The value of the `form` prop                                                                                                                           |
| `inputAttrs`       | Object                   | Object of attributes to apply to the new tag input element via `v-bind="inputAttrs"`. See below for details                                            |
| `inputHandlers`    | Object                   | Object of event handlers to apply to the new tag input element via `v-on="inputHandlers"`. See below for details                                       |
| `inputId`          | String                   | Id to add to the new tag input element. Defaults to prop `input-id`. If not provided a unique Id is auto-generated. Also available via 'inputAttrs.id' |
| `inputType`        | String                   | Type of input to render (normalized version of prop `input-type`)                                                                                      |
| `invalidTagText`   | String                   | The value of the `invalid-tag-text` prop                                                                                                               |
| `invalidTags`      | Array                    | Array of the invalid tag(s) the user has entered                                                                                                       |
| `isDuplicate`      | Boolean                  | `true` if the user input contains duplicate tag(s)                                                                                                     |
| `isInvalid`        | Boolean                  | `true` if the user input contains invalid tag(s)                                                                                                       |
| `isLimitReached`   | Boolean                  | `true` if a `limit` is configured and the amount of tags has reached the limit                                                                         |
| `limitTagsText`    | String                   | The value of the `limit-tags-text` prop                                                                                                                |
| `limit`            | String                   | The value of the `limit` prop                                                                                                                          |
| `noTagRemove`      | Boolean                  | The value of the `no-tag-remove` prop                                                                                                                  |
| `placeholder`      | String                   | The value of the `placeholder` prop                                                                                                                    |
| `removeTag`        | Function                 | Method to remove a tag. Accepts one argument which is the tag value to remove                                                                          |
| `required`         | Boolean                  | The value of the `required` prop                                                                                                                       |
| `separator`        | String                   | The value of the `separator` prop                                                                                                                      |
| `size`             | String                   | The value of the `size` prop                                                                                                                           |
| `state`            | Boolean                  | The contextual state of the component. Value of the `state` prop. Possible values are `true`, `false` or `null`                                        |
| `tagClass`         | String, Array, or Object | The value of the `tag-variant` prop. Class (or classes) to apply to the tag elements                                                                   |
| `tagPills`         | Boolean                  | The value of the `tag-pills` prop                                                                                                                      |
| `tagRemoveLabel`   | String                   | Value of the `tag-remove-label` prop. Used as the `aria-label` attribute on the remove button of tags                                                  |
| `tagVariant`       | String                   | The value of the `tag-variant` prop                                                                                                                    |
| `tags`             | Array                    | Array of current tag strings                                                                                                                           |

## `inputAttrs` object properties

The `inputAttrs` object contains attributes to bind (v-bind) to the new tag input element.

| Property   | Type    | Description                                                                  |
| ---------- | ------- | ---------------------------------------------------------------------------- |
| `disabled` | Boolean | The `disabled` attribute for the new tag input. Value of the `disabled` prop |
| `form`     | String  | The `form` attribute for the new tag input. Value of the `form` prop         |
| `id`       | String  | The `id` attribute for the new tag input                                     |
| `value`    | String  | The `value` attribute for the new tag input                                  |

The `inputAttrs` object will also include any attributes set via the `input-attrs` prop. Note that the above attributes take precedence over any of the same attributes specified in the `input-attrs` prop.

## `inputHandlers` object properties

The `inputHandlers` object contains event handlers to bind (`v-on`) to the new tag input element.

| Property  | Type     | Description                                                                                                                                                                    |
| --------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `change`  | Function | Event handler for the input element `change` event. Accepts a single argument of either an event object or a string. Change will trigger adding the tag.                       |
| `input`   | Function | Event handler for the input element `input` event. Accepts a single argument of either an event object or a string. Updates the internal v-model for the new tag input element |
| `keydown` | Function | Event handler for the input element `keydown` <kbd>Enter</kbd> and <kbd>Del</kbd> events. Accepts a single argument which is the native keydown event object                   |

The `change` handler, when needed, must be enabled via the `add-on-change` prop, otherwise it is a noop method.

## Using native browser inputs

The scope contains attributes and event handlers that can be directly bound to native `<input>` or `<select>` elements.

The following example includes the suggested ARIA attributes and roles needed for screen-reader support.

<ClientOnly>
  <b-form-tags v-model="value7" no-outer-focus class="mb-2">
    <template #default="{ tags, inputAttrs, inputHandlers, addTag, removeTag }">
      <b-input-group aria-controls="my-custom-tags-list">
        <input
          v-bind="inputAttrs"
          v-on="inputHandlers"
          placeholder="New tag - Press enter to add"
          class="form-control">
        <b-input-group-append>
          <b-button @click="addTag()" variant="primary">Add</b-button>
        </b-input-group-append>
      </b-input-group>
      <ul
        id="my-custom-tags-list"
        class="list-unstyled d-inline-flex flex-wrap mb-0"
        aria-live="polite"
        aria-atomic="false"
        aria-relevant="additions removals"
      >
        <b-card
          v-for="tag in tags"
          :key="tag"
          :id="`my-custom-tags-tag_${tag.replace(/\s/g, '_')}_`"
          tag="li"
          class="mt-1 mr-1"
          body-class="py-1 pr-2 text-nowrap"
        >
          <strong>{{ tag }}</strong>
          <b-button
            @click="removeTag(tag)"
            variant="link"
            size="sm"
            :aria-controls="`my-custom-tags-tag_${tag.replace(/\s/g, '_')}_`"
          >remove</b-button>
        </b-card>
      </ul>
    </template>
  </b-form-tags>
</ClientOnly>

```html
<template>
  <b-form-tags v-model="value" no-outer-focus class="mb-2">
    <template #default="{tags, inputAttrs, inputHandlers, addTag, removeTag}">
      <b-input-group aria-controls="my-custom-tags-list">
        <input
          v-bind="inputAttrs"
          v-on="inputHandlers"
          placeholder="New tag - Press enter to add"
          class="form-control"
        />
        <b-input-group-append>
          <b-button @click="addTag()" variant="primary">Add</b-button>
        </b-input-group-append>
      </b-input-group>
      <ul
        id="my-custom-tags-list"
        class="list-unstyled d-inline-flex flex-wrap mb-0"
        aria-live="polite"
        aria-atomic="false"
        aria-relevant="additions removals"
      >
        <!-- Always use the tag value as the :key, not the index! -->
        <!-- Otherwise screen readers will not read the tag
               additions and removals correctly -->
        <b-card
          v-for="tag in tags"
          :key="tag"
          :id="`my-custom-tags-tag_${tag.replace(/\s/g, '_')}_`"
          tag="li"
          class="mt-1 mr-1"
          body-class="py-1 pr-2 text-nowrap"
        >
          <strong>{{ tag }}</strong>
          <b-button
            @click="removeTag(tag)"
            variant="link"
            size="sm"
            :aria-controls="`my-custom-tags-tag_${tag.replace(/\s/g, '_')}_`"
            >remove</b-button
          >
        </b-card>
      </ul>
    </template>
  </b-form-tags>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const value = ref<string[]>(['apple', 'orange', 'banana', 'pear', 'peach'])
</script>
```

## Using custom form components

The scope contains attributes and event handlers that can be directly bound to most custom inputs or select components (the event handlers accept either a string tag value or a native event object). Any component that emits `input` as characters are typed, and (optionally) emits `change` when the input value changes (i.e on blur or select), and uses the prop `value` as the v-model, should work without modification.

In this example, we are using the [`<b-form-tag>` helper component](#b-form-tag-helper-component), but feel free to render tags using standard HTML or components.

<ClientOnly>
<b-form-tags v-model="value8" no-outer-focus class="mb-2">
  <template #default="{ tags, inputAttrs, inputHandlers, tagVariant, addTag, removeTag }">
    <b-input-group class="mb-2">
      <b-form-input
        v-bind="inputAttrs"
        v-on="inputHandlers"
        placeholder="New tag - Press enter to add"
        class="form-control"
      ></b-form-input>
      <b-input-group-append>
        <b-button @click="addTag()" variant="primary">Add</b-button>
      </b-input-group-append>
    </b-input-group>
    <div class="d-inline-block" style="font-size: 1.5rem;">
      <b-form-tag
        v-for="tag in tags"
        @remove="removeTag(tag)"
        :key="tag"
        :title="tag"
        :variant="tagVariant"
        class="mr-1"
      >{{ tag }}</b-form-tag>
    </div>
  </template>
</b-form-tags>
</ClientOnly>

```html
<template>
  <b-form-tags v-model="value" no-outer-focus class="mb-2">
    <template #default="{ tags, inputAttrs, inputHandlers, tagVariant, addTag, removeTag }">
      <b-input-group class="mb-2">
        <b-form-input
          v-bind="inputAttrs"
          v-on="inputHandlers"
          placeholder="New tag - Press enter to add"
          class="form-control"
        ></b-form-input>
        <b-input-group-append>
          <b-button @click="addTag()" variant="primary">Add</b-button>
        </b-input-group-append>
      </b-input-group>
      <div class="d-inline-block" style="font-size: 1.5rem;">
        <b-form-tag
          v-for="tag in tags"
          @remove="removeTag(tag)"
          :key="tag"
          :title="tag"
          :variant="tagVariant"
          class="mr-1"
          >{{ tag }}</b-form-tag
        >
      </div>
    </template>
  </b-form-tags>
</template>

<script setup lang="ts">
  import {ref} from 'vue'

  const value = ref<string[]>(['apple', 'orange', 'banana'])
</script>
```

The following is an example of using a custom select component for choosing from a pre-defined set of tags:

<ClientOnly>
<b-form-group label="Tagged input using select" label-for="tags-component-select">
  <b-form-tags
    id="tags-component-select"
    v-model="value9"
    size="lg"
    class="mb-2"
    add-on-change
    no-outer-focus
  >
    <template #default="{ tags, inputAttrs, inputHandlers, disabled, removeTag }">
      <ul v-if="tags.length > 0" class="list-inline d-inline-block mb-2">
        <li v-for="tag in tags" :key="tag" class="list-inline-item">
          <b-form-tag
            @remove="removeTag(tag)"
            :title="tag"
            :disabled="disabled"
            variant="info"
          >{{ tag }}</b-form-tag>
        </li>
      </ul>
      <b-form-select
        v-bind="inputAttrs"
        v-on="inputHandlers"
        :disabled="disabled || availableOptions.length === 0"
        :options="availableOptions"
      >
        <template #first>
          <option disabled value="">Choose a tag...</option>
        </template>
      </b-form-select>
    </template>
  </b-form-tags>
</b-form-group>
</ClientOnly>

```html
<template>
  <b-form-group label="Tagged input using select" label-for="tags-component-select">
    <!-- Prop `add-on-change` is needed to enable adding tags vie the `change` event -->
    <b-form-tags
      id="tags-component-select"
      v-model="value"
      size="lg"
      class="mb-2"
      add-on-change
      no-outer-focus
    >
      <template #default="{ tags, inputAttrs, inputHandlers, disabled, removeTag }">
        <ul v-if="tags.length > 0" class="list-inline d-inline-block mb-2">
          <li v-for="tag in tags" :key="tag" class="list-inline-item">
            <b-form-tag @remove="removeTag(tag)" :title="tag" :disabled="disabled" variant="info"
              >{{ tag }}</b-form-tag
            >
          </li>
        </ul>
        <b-form-select
          v-bind="inputAttrs"
          v-on="inputHandlers"
          :disabled="disabled || availableOptions.length === 0"
          :options="availableOptions"
        >
          <template #first>
            <!-- This is required to prevent bugs with Safari -->
            <option disabled value="">Choose a tag...</option>
          </template>
        </b-form-select>
      </template>
    </b-form-tags>
  </b-form-group>
</template>

<script setup lang="ts">
  import {ref, computed} from 'vue'

  const options = ref<string[]>([
    'Apple',
    'Orange',
    'Banana',
    'Lime',
    'Peach',
    'Chocolate',
    'Strawberry',
  ])
  const value = ref<string[]>([])

  const availableOptions = computed(() =>
    options.value.filter((opt) => value.value.indexOf(opt) === -1)
  )
</script>
```

If the custom input is using custom event names that mimic `input` and `change`, and/or needs the `.native` modifier for keydown, you can do something similar to below to bind the event handlers:

```html
<template #default="{ inputAttrs, inputHandlers, removeTag, tags }">
  <custom-input
    :id="inputAttrs.id"
    :vistom-value-prop="inputAttrs.value"
    @custom-input-event="inputHandlers.input($event)"
    @custom-change-event="inputHandlers.change($event)"
    @keydown.native="inputHandlers.keydown($event)"
  ></custom-input>
  <template v-for="tag in tags">
    <!-- Your custom tag list here -->
  </template>
</template>
```

The `inputHandlers.input` handler **must** be bound to an event that updates with each character entered by the user for the _as-you-type_ tag validation to work.

## Advanced custom rendering usage

In situations where the `inputHandlers` will not work with your custom input, or if you need greater control over tag creation, you can take advantage of the additional properties provided via the default slot's scope.

<ClientOnly>
<b-form-checkbox switch size="lg" v-model="disabled">Disable</b-form-checkbox>
<b-form-tags
v-model="value10"
@input="resetInputValue()"
tag-variant="success"
class="mb-2 mt-2"
:disabled="disabled"
no-outer-focus
placeholder="Enter a new tag value and click Add"
:state="state10">
<template v-slot="{tags, inputId, placeholder, disabled, addTag, removeTag }">
<b-input-group>
<b-form-input
      v-model="newTag"
      :id="inputId"
      :placeholder="placeholder"
      :disabled="disabled"
      :formatter="formatter"
    ></b-form-input>
<b-input-group-append>
<b-button @click="addTag(newTag)" :disabled="disabled" variant="primary">Add</b-button>
</b-input-group-append>
</b-input-group>
<b-form-invalid-feedback :state="state">
Duplicate tag value cannot be added again!
</b-form-invalid-feedback>

  <ul v-if="tags.length > 0" class="mb-0">
    <li v-for="tag in tags" :key="tag" :title="`Tag: ${tag}`" class="mt-2">
      <span  class="d-flex align-items-center">
        <span class="mr-2">{{ tag }}</span>
        <b-button
          :disabled="disabled"
          size="sm"
          variant="outline-danger"
          @click="removeTag(tag)"
        >
          remove tag
        </b-button>
      </span>
    </li>
  </ul>
  <b-form-text v-else>
    There are no tags specified. Add a new tag above.
  </b-form-text>
</template>
</b-form-tags>
</ClientOnly>

```html
<template>
  <b-form-checkbox switch size="lg" v-model="disabled">Disable</b-form-checkbox>
  <b-form-tags
    v-model="value"
    @input="resetInputValue()"
    tag-variant="success"
    class="mb-2 mt-2"
    :disabled="disabled"
    no-outer-focus
    placeholder="Enter a new tag value and click Add"
    :state="state"
  >
    <template v-slot="{tags, inputId, placeholder, disabled, addTag, removeTag }">
      <b-input-group>
        <!-- Always bind the id to the input so that it can be focused when needed -->
        <b-form-input
          v-model="newTag"
          :id="inputId"
          :placeholder="placeholder"
          :disabled="disabled"
          :formatter="formatter"
        ></b-form-input>
        <b-input-group-append>
          <b-button @click="addTag(newTag)" :disabled="disabled" variant="primary">Add</b-button>
        </b-input-group-append>
      </b-input-group>
      <b-form-invalid-feedback :state="state">
        Duplicate tag value cannot be added again!
      </b-form-invalid-feedback>
      <ul v-if="tags.length > 0" class="mb-0">
        <li v-for="tag in tags" :key="tag" :title="`Tag: ${tag}`" class="mt-2">
          <span class="d-flex align-items-center">
            <span class="mr-2">{{ tag }}</span>
            <b-button
              :disabled="disabled"
              size="sm"
              variant="outline-danger"
              @click="removeTag(tag)"
            >
              remove tag
            </b-button>
          </span>
        </li>
      </ul>
      <b-form-text v-else> There are no tags specified. Add a new tag above. </b-form-text>
    </template>
  </b-form-tags>
</template>

<script setup lang="ts">
  import {ref, computed} from 'vue'

  const newTag = ref('')
  const disabled = ref(false)
  const value = ref<string[]>([])

  const state = computed(() => (value.value.indexOf(newTag.value.trim()) > -1 ? false : null))

  function resetInputValue() {
    newTag.value = ''
  }

  function formatter(value) {
    return value.value.toUpperCase()
  }
</script>
```

The following is an example of using the `<b-dropdown>` component for choosing or searching from a pre-defined set of tags:

<ClientOnly>
  <b-form-group label="Tagged input using dropdown" label-for="tags-with-dropdown">
  <b-form-tags id="tags-with-dropdown" v-model="value11" no-outer-focus class="mb-2">
  <template v-slot="{ tags, disabled, addTag, removeTag }">
    <ul v-if="tags.length > 0" class="list-inline d-inline-block mb-2">
      <li v-for="tag in tags" :key="tag" class="list-inline-item">
        <b-form-tag
          @remove="removeTag(tag)"
          :title="tag"
          :disabled="disabled"
          variant="info"
        >{{ tag }}</b-form-tag>
      </li>
    </ul>
    <b-dropdown size="sm" variant="outline-secondary" block menu-class="w-100">
      <template #button-content>
        Choose tags
      </template>
      <b-dropdown-form @submit.stop.prevent="() => {}">
        <b-form-group
          label="Search tags"
          label-for="tag-search-input"
          label-cols-md="auto"
          class="mb-0"
          label-size="sm"
          :description="searchDesc"
          :disabled="disabled"
        >
          <b-form-input
            v-model="search"
            id="tag-search-input"
            type="search"
            size="sm"
            autocomplete="off"
            ></b-form-input>
        </b-form-group>
      </b-dropdown-form>
      <b-dropdown-divider></b-dropdown-divider>
      <b-dropdown-item-button
        v-for="option in availableOptions11"
        :key="option"
        @click="onOptionClick({ option, addTag })"
      >
        {{ option }}
      </b-dropdown-item-button>
      <b-dropdown-text v-if="availableOptions11.length === 0">
        There are no tags available to select
      </b-dropdown-text>
    </b-dropdown>
  </template>
  </b-form-tags>
  </b-form-group>
</ClientOnly>

```html
<template>
  <b-form-group label="Tagged input using dropdown" label-for="tags-with-dropdown">
    <b-form-tags id="tags-with-dropdown" v-model="value" no-outer-focus class="mb-2">
      <template v-slot="{ tags, disabled, addTag, removeTag }">
        <ul v-if="tags.length > 0" class="list-inline d-inline-block mb-2">
          <li v-for="tag in tags" :key="tag" class="list-inline-item">
            <b-form-tag @remove="removeTag(tag)" :title="tag" :disabled="disabled" variant="info"
              >{{ tag }}</b-form-tag
            >
          </li>
        </ul>

        <b-dropdown size="sm" variant="outline-secondary" block menu-class="w-100">
          <template #button-content> Choose tags </template>
          <b-dropdown-form @submit.stop.prevent="() => {}">
            <b-form-group
              label="Search tags"
              label-for="tag-search-input"
              label-cols-md="auto"
              class="mb-0"
              label-size="sm"
              :description="searchDesc"
              :disabled="disabled"
            >
              <b-form-input
                v-model="search"
                id="tag-search-input"
                type="search"
                size="sm"
                autocomplete="off"
              ></b-form-input>
            </b-form-group>
          </b-dropdown-form>
          <b-dropdown-divider></b-dropdown-divider>
          <b-dropdown-item-button
            v-for="option in availableOptions"
            :key="option"
            @click="onOptionClick({ option, addTag })"
          >
            {{ option }}
          </b-dropdown-item-button>
          <b-dropdown-text v-if="availableOptions.length === 0">
            There are no tags available to select
          </b-dropdown-text>
        </b-dropdown>
      </template>
    </b-form-tags>
  </b-form-group>
</template>

<script setup lang="ts">
  import {ref, computed} from 'vue'

  const options = ref(['Apple', 'Orange', 'Banana', 'Lime', 'Peach', 'Chocolate', 'Strawberry'])
  const search = ref('')
  const value = ref([])

  const criteria = computed(() => {
    // Compute the search criteria
    return search.value.trim().toLowerCase()
  })
  const availableOptions = computed(() => {
    // Filter out already selected options
    const options = options.value.filter((opt) => value.value.indexOf(opt) === -1)

    if (criteria.value) {
      // Show only options that match criteria
      return options.filter((opt) => opt.toLowerCase().indexOf(criteria.value) > -1)
    }
    // Show all options available
    return options
  })

  const searchDesc = computed(() => {
    if (criteria.value && availableOptions.value.length === 0) {
      return 'There are no tags matching your search criteria'
    }

    return ''
  })

  function onOptionClick({option, addTag}) {
    addTag(option)
    search.value = ''
  }
</script>
```

## Creating wrapper components

You can easily create a custom wrapper component with your preferred rendering style as follows:

```html
<template>
  <b-form-tags :value="value" @input="$emit('input', $event)">
    <template #default="{ tags, addTag, removeTag, inputAttrs, inputHandlers }">
      <!-- Place your custom rendering here -->
    </template>
  </b-form-tags>
</template>

<script setup lang="ts">
  import {BFormTags} from 'bootstrap-vue-3'

  defineProps({
    value: {type: Array, default: () => []},
  })
</script>
```

## `<b-form-tag>` helper component

BootstrapVue provides the helper component `<b-form-tag>`, for use with the default scoped slot of `<b-form-tags>`. The component is based upon [`<b-badge>`](Badge.md) and [`<b-button-close>`](ButtonClose.md).

`<b-form-tag>` supports the same variants as `<b-badge>` and also supports `pill` styling. Sizing is based on the containing element's font-size.

The remove event is emitted when the `<b-form-tag>` remove button is clicked.

Tags that are too wide for their parent container will automatically have their text content truncated with an ellipsis. For this reason, it is always good practice to supply a title via the title prop when using the default slot of `<b-form-tag>` for the tag content.

Note `<b-form-tag>` requires BootstrapVue3's custom CSS/SCSS for proper styling.

<ClientOnly>
  <doc-reference :reference="FormTags" />
</ClientOnly>

<script setup lang="ts">
  import { ref, computed, watch } from 'vue'
  import FormTags from '../../../../component-references/FormTags.json'

  // #1 example
  const value = ref(['apple', 'orange'])
  
  // #2 example
  const value2 = ref(['one', 'two'])
  
  // #3 example
  const value3 = ref(['apple', 'orange', 'grape'])
  
  // #4 example
  const tags = ref([])
  const dirty = ref(false)

  const state = computed(() => dirty.value ? (tags.value.length > 2 && tags.value.length < 9) : null)

  watch(tags, () => dirty.value = false)

  function tagValidator(tag) {
    return tag === tag.toLowerCase() && tag.length > 2 && tag.length < 6
  }
  
  // #5 example
  const tags5 = ref([])
  const validTags = ref([])
  const invalidTags = ref([])
  const duplicateTags = ref([])

  function onTagState(valid, invalid, duplicate) {
    validTags.value = valid
    invalidTags.value = invalid
    duplicateTags.value = duplicate
  }

  function validator(tag) {
    return tag.length > 2 && tag.length < 6
  }

  // #6 example
  const value6 = ref([])
  const limit = ref(5)

  // #7 example
  const value7 = ref(['apple', 'orange', 'banana', 'pear', 'peach'])

  // #8 example
  const value8 = ref(['apple', 'orange', 'banana'])

  // #9 example
  const options = ref(['Apple', 'Orange', 'Banana', 'Lime', 'Peach', 'Chocolate', 'Strawberry'])
  const value9 = ref([])
  const availableOptions = computed(() => options.value.filter(opt => value.value.indexOf(opt) === -1));

  // #10 example
  const newTag = ref('')
  const disabled = ref(false)
  const value10 = ref([])

  const state10 = computed(() => value.value.indexOf(newTag.value.trim()) > -1 ? false : null)

  function resetInputValue() {
    newTag.value = ''
  }

  function formatter(value) {
    return value.value.toUpperCase()
  }

  // #11 example
  const options11 = ref(['Apple', 'Orange', 'Banana', 'Lime', 'Peach', 'Chocolate', 'Strawberry'])
  const search = ref('')
  const value11 = ref([])

  const criteria = computed(() => {
    return search.value.trim().toLowerCase()
  })
  const availableOptions11 = computed(() => {
    const _options = options.value.filter(opt => value.value.indexOf(opt) === -1)
    
    if (criteria.value) {
      return _options.filter(opt => opt.toLowerCase().indexOf(criteria.value) > -1);
    }
    
    return _options
  })
  const searchDesc = computed(() => {
    if (criteria.value && availableOptions11.value.length === 0) {
      return 'There are no tags matching your search criteria'
    }
    return ''
  })

  function onOptionClick({ option, addTag }) {
    addTag(option)
    search.value = ''
  }
  </script>
