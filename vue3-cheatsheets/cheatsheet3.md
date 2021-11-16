## cheatsheet

### Using slots and named slots to place data inside your components
```typescript
<template>
  <div class="cardBox elevation_2">
    <div class="header">
      <slot
        v-if="$slots.header"
        name="header"
        />
      <div v-else>
        <h1 class="cardHeader cardText">
          Card Header
        </h1>
        <h2 class="cardSubHeader cardText">
          Card Sub Header
        </h2>
      </div>
    </div>
    <div class="media">
      <slot
        v-if="$slots.media"
        name="media"
      />
      <img v-else
        src="https://via.placeholder.com/350x250"
      >
    </div> 
    <div
      v-if="$slots.default"
      class="section cardText"
      :class="{
        noBottomPadding: $slots.action,
        halfPaddingTop: $slots.media,
      }">
    <slot />
  </div> 
  <div
    v-if="$slots.action"
    class="action"
  >
    <slot name="action" />
  </div>
  </div>
</template>


<template>
  <div id="app">
    <MaterialCardBox>
      <template v-slot:header>
        <strong>Card Title</strong><br>
        <span>Card Sub-Title</span>
      </template>
      <template v-slot:media>
        <img src="https://via.placeholder.com/350x150">
      </template>
      <p>Main Section</p>
      <template v-slot:action>
        <button>Action Button</button>
        <button>Action Button</button>
      </template>
    </MaterialCardBox>
  </div>
</template>
```

### Passing data to your component and validating the data
```typescript
<script>
  export default {
    name: 'MaterialCardBox',
    inheritAttrs: false,
    props: {
      header: {
        type: String,
        required: false,
        default: '',
        validator: v => typeof v === 'string',
      },
      subHeader: {
        type: String,
        required: false,
        default: '',
        validator: v => typeof v === 'string',
      },
      mainText: {
        type: String,
        required: false,
        default: '',
        validator: v => typeof v === 'string',
      },
      showMedia: {
        type: Boolean,
        required: false,
        default: false,
        validator: v => typeof v === 'boolean',
      }, 
      imgSrc: {
        type: String,
        required: false,
        default: '',
        validator: v => typeof v === 'string',
      },
      showActions: {
        type: Boolean,
        required: false,
        default: false,
        validator: v => typeof v === 'boolean',
      },
      elevation: {
        type: Number,
        required: false,
        default: 2,
        validator: v => typeof v === 'number',
      }, 
    },
    computed: {
      showMediaContent() {
        return (this.$slots.media || this.imgSrc) && this.showMedia;
      },
      showActionsButtons() {
        return this.showActions && this.$slots.action;
      },
      showHeader() {
        return this.$slots.header || (this.header || this.subHeader);
      },
      showMainContent() {
        return this.$slots.default || this.mainText;
      },
      cardElevation() {
        return `elevation_${parseInt(this.elevation, 10)}`;
      }, 
    },
  },
};
</script>
```

### Creating functional components
```typescript
<script>
  import isColor from 'is-color';
  export default {
    name: 'MaterialButton',
    props: {
      backgroundColor: {
        type: String,
        required: false,
        default: '#fff',
        validator: v => typeof v === 'string' && isColor(v),
      },
      textColor: {
        type: String,
        required: false,
        default: '#000',
        validator: v => typeof v === 'string' && isColor(v),
      }, 
      isRound: {
        type: Boolean,
        required: false,
        default: false,
      }, 
      isFlat: {
        type: Boolean,
        required: false,
        default: false,
      }, 
    },
  }; 
</script>

<template functional>
  <button
    tabindex="0"
    class="button"
    :class="{
      round: props.isRound,
      isFlat: props.isFlat,
    }" 
    :style="{
      background: props.backgroundColor,
      color: props.textColor
    }"
    v-on="listeners"
  >
    <div
      tabindex="-1"
      class="button_focus_helper"
    />
    <slot/>
  </button>
</template>
```

### Accessing your children components data
```typescript
<script>
  export default {
    name: 'StarRatingInput',
    props: {
      maxRating: {
        type: Number,
        required: false,
        default: 5,
      }, 
    },
    data: () => ({
      rating: 0,
    }),
    methods: {
      updateRating(value) {
        this.rating = value;
      },
      emitFinalVote(value) {
        this.updateRating(value);
        this.$emit('final-vote', this.rating);
      },
      getStarName(rate) {
        if (rate <= this.rating) {
          return 'star';
        }
        if (Math.fround((rate - this.rating)) < 1) {
          return 'star_half';
        }
        return 'star_border';
      },
    }, 
  };
</script>


<template>
  <div class="starRating">
    <span class="rateThis">
      <slot />
    </span> 
    <ul>
      <li
        v-for="rate in maxRating"
        :key="rate"
        @mouseenter="updateRating(rate)"
        @click="emitFinalVote(rate)"
        @focus="updateRating(rate)"
      >
        <i class="material-icons">
          {{ getStarName(rate) }}
        </i>
      </li> 
    </ul>
  </div>
</template>
```

### Creating a dynamic injected component
```typescript
 <script>
  import StarRatingInput from './StarRatingInput.vue';
  import StarRatingDisplay from './StarRatingDisplay.vue';
  export default {
    name: 'StarRating',
    components: { StarRatingDisplay, StarRatingInput },
    props: {
      maxRating: {
        type: Number,
        required: false,
        default: 5,
      }, 
      rating: {
        type: Number,
        required: false,
        default: 0,
      }, 
      votes: {
        type: Number,
        required: false,
        default: 0,
      }, 
    },
    data: () => ({
      rank: 0,
      voted: false,
    }),
    computed: {
      starComponent() {
        if (!this.voted) return StarRatingInput;
          return StarRatingDisplay;
      },
    },
    methods: {
      vote(rank) {
        this.rank = rank;
        this.voted = true;
      },
    }, 
  };
</script>

<template>
  <component
    :is="starComponent"
    :max-rating="maxRating"
    :rating="rating || rank"
    :votes="votes"
    @final-vote="vote"
  >
    Rate this Place
  </component>
</template>
```

### Creating a dependency injection component
```typescript
<script>
  import StarRatingInput from './StarRatingInput.vue';
  import StarRatingDisplay from './StarRatingDisplay.vue';
  export default {
    name: 'StarRating',
    components: { StarRatingDisplay, StarRatingInput },
    provide: {
      starRating: true,
    },
    props: {
      maxRating: {
        type: Number,
        required: false,
        default: 5,
      }, 
      rating: {
        type: Number,
        required: false,
        default: 0,
      }, 
      votes: {
        type: Number,
        required: false,
        default: 0,
      }, 
    },
    data: () => ({
      rank: 0,
      voted: false,
    }),
    computed: {
      starComponent() {
        if (!this.voted) return StarRatingInput;
        return StarRatingDisplay;
      },
    }, 
    methods: {
      vote(rank) {
        this.rank = rank;
           this.voted = true;
      },
    }, 
  };
</script>


<script>
  export default {
    name: 'StarRatingDisplay',
    props: {
      maxRating: {
        type: Number,
        required: false,
        default: 5,
      }, 
      rating: {
        type: Number,
        required: false,
        default: 0,
      }, 
      votes: {
        type: Number,
        required: false,
        default: 0,
      }, 
    },
    inject: {
      starRating: {
        default() {
          console.error('StarRatingDisplay need to be a child of
            StarRating');
        },
      }, 
    },
    methods: {
      updateRating(value) {
        this.rating = value;
      },
      emitFinalVote(value) {
        this.updateRating(value);
        this.$emit('final-vote', this.rating);
      },
      getStarName(rate) {
        if (rate <= this.rating) {
          return 'star';
        }
        if (Math.fround((rate - this.rating)) < 1) {
             return 'star_half';
        }
        return 'star_border';
      },
    }, 
  };
</script>
```

### Creating a component mixin
```typescript
export default {
  props: {
    maxRating: {
      type: Number,
      required: false,
      default: 5,
    }, 
    rating: {
      type: Number,
      required: false,
        default: 0,
      },
    votes: {
      type: Number,
      required: false,
      default: 0,
    }, 
  },
};

export default {
  methods: {
    getStarName(rate) {
      if (rate <= this.rating) {
        return 'star';
      }
      if (Math.fround((rate - this.rating)) < 1) {
        return 'star_half';
      }
      return 'star_border';
    },
  }, 
};

<script>
  import StarRatingBaseMixin from '../mixins/starRatingBase';
  import StarRatingChildMixin from '../mixins/starRatingChild';
  export default {
    name: 'StarRatingInput',
    mixins: [
      StarRatingBaseMixin,
      StarRatingChildMixin,
    ],
    data: () => ({
      rank: 0,
    }),
    methods: {
      updateRating(value) {
        this.rank = value;
      },
      emitFinalVote(value) {
        this.updateRating(value);
        this.$emit('final-vote', this.rank);
      },
      getStarName(rate) {
        const rating = (this.rating || this.rank);
        if (rate <= rating) {
          return 'star';
        }
        if (Math.fround((rate - rating)) < 1) {
          return 'star_half';
        }     
        return 'star_border';
      },
    }, 
  };
</script>

```

### Lazy loading your components
```typescript
<script>
  export default {
    name: 'App',
    components: {
      StarRating: () => import('./components/StarRating.vue'),
      MaterialButton: () =>
        import('./components/MaterialButton.vue'),
      MaterialCardBox: () =>
        import('./components/MaterialCardBox.vue'),
    }, 
    methods: {
      resetVote() {
        this.$refs.starRating.rank = 0;
        this.$refs.starRating.voted = false;
      },
      forceVote() {
        this.$refs.starRating.rank = 5;
        this.$refs.starRating.voted = true;
      }, 
    },
  }; 
</script>

```