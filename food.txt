const recipeForm = document.getElementById("recipeForm");
const recipesContainer = document.getElementById("recipesContainer");
const searchInput = document.getElementById("searchInput");
const favoritesContainer = document.getElementById("favoritesContainer");

let recipes = JSON.parse(localStorage.getItem("recipes")) || [];
let editIndex = null;

// Display recipes
function displayRecipes(filteredRecipes = recipes) {
    recipesContainer.innerHTML = "";
    filteredRecipes.forEach((recipe, index) => {
        const recipeCard = document.createElement("div");
        recipeCard.classList.add("recipe");

        const averageRating = calculateAverageRating(recipe.ratings || []);

        recipeCard.innerHTML = `
            <img src="${recipe.image || 'placeholder.jpg'}" alt="${recipe.name}">
            <div class="content">
                <h3>${recipe.name}</h3>
                <p><strong>Ingredients:</strong> ${recipe.ingredients}</p>
                <p><strong>Average Rating:</strong> ${averageRating} ⭐</p>
                <div class="rating">
                    <span onclick="rateRecipe(${index}, 1)">⭐</span>
                    <span onclick="rateRecipe(${index}, 2)">⭐⭐</span>
                    <span onclick="rateRecipe(${index}, 3)">⭐⭐⭐</span>
                    <span onclick="rateRecipe(${index}, 4)">⭐⭐⭐⭐</span>
                    <span onclick="rateRecipe(${index}, 5)">⭐⭐⭐⭐⭐</span>
                </div>
                <button onclick="viewRecipe(${index})">View Details</button>
                <button onclick="editRecipe(${index})">Edit</button>
                <button onclick="deleteRecipe(${index})">Delete</button>
                <button onclick="toggleFavorite(${index})">${recipe.isFavorite ? "Unfavorite" : "Favorite"}</button>
            </div>
        `;
        recipesContainer.appendChild(recipeCard);
    });
}

// Display favorites
function displayFavorites() {
    favoritesContainer.innerHTML = "";
    const favoriteRecipes = recipes.filter(recipe => recipe.isFavorite);

    if (favoriteRecipes.length === 0) {
        favoritesContainer.innerHTML = "<p>No favorite recipes yet.</p>";
        return;
    }

    favoriteRecipes.forEach((recipe, index) => {
        const recipeCard = document.createElement("div");
        recipeCard.classList.add("recipe");

        const averageRating = calculateAverageRating(recipe.ratings || []);

        recipeCard.innerHTML = `
            <img src="${recipe.image || 'placeholder.jpg'}" alt="${recipe.name}">
            <div class="content">
                <h3>${recipe.name}</h3>
                <p><strong>Ingredients:</strong> ${recipe.ingredients}</p>
                <p><strong>Average Rating:</strong> ${averageRating} ⭐</p>
                <button onclick="viewRecipe(${recipes.indexOf(recipe)})">View Details</button>
                <button onclick="toggleFavorite(${recipes.indexOf(recipe)})">Unfavorite</button>
            </div>
        `;
        favoritesContainer.appendChild(recipeCard);
    });
}

// Calculate average rating
function calculateAverageRating(ratings) {
    if (ratings.length === 0) return "No ratings yet";
    const total = ratings.reduce((sum, rating) => sum + rating, 0);
    return (total / ratings.length).toFixed(1);
}

// Rate recipe
function rateRecipe(index, rating) {
    if (!recipes[index].ratings) recipes[index].ratings = [];
    recipes[index].ratings.push(rating);
    localStorage.setItem("recipes", JSON.stringify(recipes));
    displayRecipes();
    displayFavorites();
}

// Toggle favorite
function toggleFavorite(index) {
    recipes[index].isFavorite = !recipes[index].isFavorite;
    localStorage.setItem("recipes", JSON.stringify(recipes));
    displayRecipes();
    displayFavorites();
}

// Add recipe
function addRecipe(event) {
    event.preventDefault();

    const name = document.getElementById("recipeName").value;
    const ingredients = document.getElementById("ingredients").value;
    const steps = document.getElementById("steps").value;
    const imageInput = document.getElementById("imageUpload");
    const image = imageInput.files[0] ? URL.createObjectURL(imageInput.files[0]) : "";

    const newRecipe = { name, ingredients, steps, image, ratings: [], isFavorite: false };

    if (editIndex !== null) {
        recipes[editIndex] = newRecipe;
        editIndex = null;
    } else {
        recipes.push(newRecipe);
    }

    localStorage.setItem("recipes", JSON.stringify(recipes));
    recipeForm.reset();
    displayRecipes();
    displayFavorites();
}

// View recipe details
function viewRecipe(index) {
    const recipe = recipes[index];
    alert(`Name: ${recipe.name}\n\nIngredients: ${recipe.ingredients}\n\nSteps: ${recipe.steps}`);
}

// Edit recipe
function editRecipe(index) {
    const recipe = recipes[index];

    document.getElementById("recipeName").value = recipe.name;
    document.getElementById("ingredients").value = recipe.ingredients;
    document.getElementById("steps").value = recipe.steps;

    editIndex = index;
}

// Delete recipe
function deleteRecipe(index) {
    if (confirm("Are you sure you want to delete this recipe?")) {
        recipes.splice(index, 1);
        localStorage.setItem("recipes", JSON.stringify(recipes));
        displayRecipes();
        displayFavorites();
    }
}

// Search recipes
function searchRecipes() {
    const query = searchInput.value.toLowerCase();
    const filteredRecipes = recipes.filter(recipe =>
        recipe.name.toLowerCase().includes(query) ||
        recipe.ingredients.toLowerCase().includes(query)
    );
    displayRecipes(filteredRecipes);
}

// Event listeners
recipeForm.addEventListener("submit", addRecipe);
searchInput.addEventListener("input", searchRecipes);

// Initial display
displayRecipes();
displayFavorites();



// Existing code stays the same

function displayRecipes(filteredRecipes = recipes) {
    recipesContainer.innerHTML = "";
    filteredRecipes.forEach((recipe, index) => {
        const recipeCard = document.createElement("div");
        recipeCard.classList.add("recipe");

        const averageRating = calculateAverageRating(recipe.ratings || []);

        recipeCard.innerHTML = `
            <img src="${recipe.image || 'placeholder.jpg'}" alt="${recipe.name}">
            <div class="content">
                <h3>${recipe.name}</h3>
                <p><strong>Ingredients:</strong> ${recipe.ingredients}</p>
                <p><strong>Average Rating:</strong> ${averageRating} ⭐</p>
                <div class="rating" id="rating-${index}">
                    <span onclick="rateRecipe(${index}, 1)">⭐</span>
                    <span onclick="rateRecipe(${index}, 2)">⭐⭐</span>
                    <span onclick="rateRecipe(${index}, 3)">⭐⭐⭐</span>
                    <span onclick="rateRecipe(${index}, 4)">⭐⭐⭐⭐</span>
                    <span onclick="rateRecipe(${index}, 5)">⭐⭐⭐⭐⭐</span>
                </div>
                <button onclick="viewRecipe(${index})">View Details</button>
                <button onclick="editRecipe(${index})">Edit</button>
                <button onclick="confirmDelete(${index})">Delete</button>
                <button onclick="toggleFavorite(${index})">${recipe.isFavorite ? "Unfavorite" : "Favorite"}</button>
            </div>
        `;
        recipesContainer.appendChild(recipeCard);
    });
}

function confirmDelete(index) {
    if (confirm("Are you sure you want to delete this recipe? This action cannot be undone.")) {
        deleteRecipe(index);
    }
}

function deleteRecipe(index) {
    recipes.splice(index, 1);
    localStorage.setItem("recipes", JSON.stringify(recipes));
    displayRecipes();
    displayFavorites();
}

// New hover effect for stars
const ratingSpans = document.querySelectorAll('.rating span');
ratingSpans.forEach(span => {
    span.addEventListener('mouseover', function() {
        const ratingIndex = this.innerText.length;
        const ratingContainer = this.closest('.rating');
        for (let i = 0; i < ratingIndex; i++) {
            ratingContainer.children[i].style.color = '#FF9900';
        }
        for (let i = ratingIndex; i < ratingContainer.children.length; i++) {
            ratingContainer.children[i].style.color = '';
        }
    });
    span.addEventListener('mouseout', function() {
        const ratingContainer = this.closest('.rating');
        Array.from(ratingContainer.children).forEach(child => child.style.color = '');
    });
});



