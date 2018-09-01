<template>
	<div class="card" itemscope>
		<div class="card__favorite star" @click="$emit('addToFavorites', card.name)"
			 :class="{'checked': isInFavorites}">
			<svg aria-hidden="true" data-prefix="far" data-icon="star"
				 class="svg-inline--fa fa-star fa-w-18 star__border" role="img"
				 xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512">
				<path fill="currentColor"
					  d="M528.1 171.5L382 150.2 316.7 17.8c-11.7-23.6-45.6-23.9-57.4 0L194 150.2 47.9 171.5c-26.2 3.8-36.7 36.1-17.7 54.6l105.7 103-25 145.5c-4.5 26.3 23.2 46 46.4 33.7L288 439.6l130.7 68.7c23.2 12.2 50.9-7.4 46.4-33.7l-25-145.5 105.7-103c19-18.5 8.5-50.8-17.7-54.6zM388.6 312.3l23.7 138.4L288 385.4l-124.3 65.3 23.7-138.4-100.6-98 139-20.2 62.2-126 62.2 126 139 20.2-100.6 98z"></path>
			</svg>
			<svg aria-hidden="true" data-prefix="fas" data-icon="star"
				 class="svg-inline--fa fa-star fa-w-18 star__background" role="img"
				 xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512">
				<path fill="currentColor"
					  d="M259.3 17.8L194 150.2 47.9 171.5c-26.2 3.8-36.7 36.1-17.7 54.6l105.7 103-25 145.5c-4.5 26.3 23.2 46 46.4 33.7L288 439.6l130.7 68.7c23.2 12.2 50.9-7.4 46.4-33.7l-25-145.5 105.7-103c19-18.5 8.5-50.8-17.7-54.6L382 150.2 316.7 17.8c-11.7-23.6-45.6-23.9-57.4 0z"></path>
			</svg>
		</div>

		<div class="card__image">
			<img itemprop="image" :src="`./../assets/${card.img}`" :alt="card.name">
		</div>

		<div class="card__content">
			<h2 class="card__title" itemprop="name"> {{ card.name }}</h2>

			<div class="card__price price" itemprop="price">
				<span class="price__current" v-if="card.current_price">
					${{ card.current_price }}
				</span>
				<span class="price__old" v-if="card.old_price" :class="{ 'price__old--discount': card.old_price }">
					${{ card.old_price }}
				</span>
				<span class="price__noPrice" v-if="!card.current_price && !card.old_price">not available</span>
			</div>

			<button
					class="btn card__action btn--add"
					@click="$emit('addToCard', card.name)"
					v-if="isInCart"
			>
				Add to Cart
			</button>
			<a
					itemprop="url"
					href="#"
					class="btn card__action btn--checkout"
					v-else
			>
				Checkout
			</a>
		</div>
	</div>
</template>

<script>
    export default {
        name: 'Card',
        props: {
            card: Object,
            isInCart: Boolean,
            isInFavorites: Boolean
        }
    }
</script>

<style scoped lang="scss">
	@import "../scss/common/helpers";

	.card {
		flex: 0 0 100%;
		margin: 0 15px 30px;

		@include for-tablet-up {
			flex: 0 0 calc(50% - 30px);
			margin: 0 15px 30px;
		}

		@include for-desktop-up {
			flex: 0 0 calc(25% - 30px);
			margin: 0 15px 30px;
		}
	}

	.btn {
		width: 100%;
	}

</style>
