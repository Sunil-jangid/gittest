# gittest
<div>
  <h2>Add Ingredients to Meal</h2>

  <!-- Search for ingredients -->
  <label for="ingredient-search">Search for an ingredient</label>
  <input type="text" id="ingredient-search" [(ngModel)]="searchTerm" (input)="searchIngredients()">
  
  <!-- Display search results -->
  <ul *ngIf="searchResults.length > 0">
    <li *ngFor="let ingredient of searchResults">
      {{ ingredient.ingredientName }} 
      <button (click)="addIngredient(ingredient)">Add</button>
    </li>
  </ul>

  <!-- List of selected ingredients -->
  <h3>Selected Ingredients</h3>
  <table>
    <thead>
      <tr>
        <th>Ingredient Name</th>
        <th>Quantity</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let ingredient of selectedIngredients">
        <td>{{ ingredient.ingredientName }}</td>
        <td>
          <input type="number" [(ngModel)]="ingredient.quantity">
        </td>
        <td>
          <button (click)="removeIngredient(ingredient)">Remove</button>
        </td>
      </tr>
    </tbody>
  </table>

  <!-- Button to finalize meal -->
  <button (click)="finalizeMeal()">Save Meal</button>
</div>
import { Component, OnInit } from '@angular/core';
import { Ingredient } from './ingredient.model'; // Your ingredient model
import { MealsService } from './meals.service'; // Your service to fetch ingredients

@Component({
  selector: 'app-meal',
  templateUrl: './meal.component.html',
  styleUrls: ['./meal.component.css']
})
export class MealComponent implements OnInit {
  searchTerm: string = '';
  searchResults: Ingredient[] = []; // Search result from the database
  selectedIngredients: { ingredientName: string, ingredientId: number, quantity: number }[] = []; // Ingredients added to the meal

  constructor(private mealsService: MealsService) {}

  ngOnInit(): void {
    // You can load initial data if needed
  }

  // Search for ingredients based on the user's input
  searchIngredients() {
    if (this.searchTerm.trim() === '') {
      this.searchResults = [];
      return;
    }

    this.mealsService.searchIngredients(this.searchTerm).subscribe((results: Ingredient[]) => {
      this.searchResults = results;
    });
  }

  // Add an ingredient to the selected list
  addIngredient(ingredient: Ingredient) {
    this.selectedIngredients.push({
      ingredientId: ingredient.ingredientId,
      ingredientName: ingredient.ingredientName,
      quantity: 1 // Default quantity
    });

    // Clear search
    this.searchResults = [];
    this.searchTerm = '';
  }

  // Remove an ingredient from the list
  removeIngredient(ingredient: any) {
    this.selectedIngredients = this.selectedIngredients.filter(i => i.ingredientId !== ingredient.ingredientId);
  }

  // Finalize the meal and send it to the backend
  finalizeMeal() {
    // Here you can send selectedIngredients to the backend
    console.log('Meal with ingredients', this.selectedIngredients);

    // Save meal using a service or API call
    // mealsService.saveMeal(selectedIngredients).subscribe(...)
  }
}

searchIngredients(term: string): Observable<Ingredient[]> {
  return this.http.get<Ingredient[]>(`${this.baseUrl}/ingredients/search?name=${term}`);
}


@GetMapping("/ingredients/search")
public List<Ingredient> searchIngredients(@RequestParam String name) {
    return ingredientService.searchByName(name); // Implement search logic in service
}


/* Styling for the Add Ingredients section */
.add-ingredients-container {
  margin: 20px 0;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #f9f9f9;
  max-width: 600px;
}

.add-ingredients-container h2 {
  margin-bottom: 20px;
  font-size: 24px;
  color: #333;
}

.add-ingredients-container label {
  font-size: 16px;
  margin-right: 10px;
}

.add-ingredients-container input {
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 10px;
}

.add-ingredients-container button {
  padding: 8px 12px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.add-ingredients-container button:hover {
  background-color: #0056b3;
}

/* Styling for the ingredients list */
.ingredient-list {
  list-style-type: none;
  padding: 0;
  margin-top: 20px;
}

.ingredient-list li {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  padding: 8px;
  border-bottom: 1px solid #ddd;
}

.ingredient-list li button {
  background-color: #28a745;
  color: white;
  padding: 4px 8px;
  border: none;
  border-radius: 4px;
}

.ingredient-list li button:hover {
  background-color: #218838;
}

/* Styling for the selected ingredients table */
.table-container {
  margin-top: 20px;
}

.table-container table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.table-container th,
.table-container td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.table-container th {
  background-color: #f2f2f2;
  font-size: 18px;
}

.table-container td input {
  width: 60px;
  padding: 5px;
}

.table-container td button {
  padding: 5px 10px;
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.table-container td button:hover {
  background-color: #c82333;
}

/* Save button styling */
.save-button {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.save-button:hover {
  background-color: #0056b3;
}

/* Responsive design */
@media (max-width: 768px) {
  .add-ingredients-container,
  .table-container {
    width: 100%;
  }

  .ingredient-list li {
    flex-direction: column;
    align-items: flex-start;
  }

  .ingredient-list li button {
    margin-top: 5px;
  }
}



<div class="diet-container">
  <!-- Buttons to switch sections -->
  <div>
    <button mat-raised-button color="primary" (click)="showSection('diet')">Diet</button>
    <button mat-raised-button color="accent" (click)="showSection('meal')">Meals</button>
    <button mat-raised-button color="warn" (click)="showSection('ingredient')">Ingredient</button>
  </div>

  <!-- Diet Section -->
  <div *ngIf="selectedSection === 'diet'">
    <mat-form-field appearance="fill">
      <mat-label>Diet Name</mat-label>
      <input matInput id="dietName" formControlName="dietName" />
    </mat-form-field>
  </div>

  <!-- Meal Section -->
  <div *ngIf="selectedSection === 'meal'">
    <mat-tab-group>
      <mat-tab label="Create Meal">
        <div class="meal-container">
          <form (ngSubmit)="addMeal()" #mealForm="ngForm">
            <mat-form-field appearance="fill" class="form-group">
              <mat-label>Meal Name</mat-label>
              <input matInput [(ngModel)]="mealName" name="mealName" required placeholder="Enter the Name of meal">
            </mat-form-field>
            
            <mat-form-field appearance="fill">
              <mat-label>Meal Description</mat-label>
              <input matInput [(ngModel)]="mealDescription" name="mealDescription" required placeholder="Enter the Description">
            </mat-form-field>

            <mat-form-field appearance="fill">
              <mat-label>Calories</mat-label>
              <input matInput type="number" [(ngModel)]="calories" name="calories" required placeholder="Enter Amount of calories">
            </mat-form-field>

            <mat-form-field appearance="fill">
              <mat-label>Protein</mat-label>
              <input matInput type="number" [(ngModel)]="protein" name="protein" required placeholder="Enter Amount of protein">
            </mat-form-field>

            <mat-form-field appearance="fill">
              <mat-label>Fiber</mat-label>
              <input matInput type="number" [(ngModel)]="fibre" name="fibre" required placeholder="Enter Amount of fiber">
            </mat-form-field>

            <mat-form-field appearance="fill">
              <mat-label>Fat</mat-label>
              <input matInput type="number" [(ngModel)]="fat" name="fat" required placeholder="Enter Amount of Fat">
            </mat-form-field>

            <button mat-raised-button color="primary" type="submit" [disabled]="!mealForm.form.valid">Add Meal</button>
          </form>
        </div>
      </mat-tab>

      <!-- Add Ingredients Section -->
      <mat-tab label="Add Ingredients">
        <div *ngIf="isMealAdded" class="add-ingredients-container">
          <mat-form-field appearance="fill">
            <mat-label>Search Ingredient</mat-label>
            <input matInput type="text" [(ngModel)]="searchTerm" (input)="searchIngredients()" placeholder="Search for an Ingredient">
          </mat-form-field>

          <mat-list>
            <mat-list-item *ngFor="let ingredient of searchResults">
              <span matLine>{{ ingredient.ingredientName }}</span>
              <mat-form-field>
                <mat-label>Quantity</mat-label>
                <input matInput type="number" [(ngModel)]="ingredient.quantity" placeholder="Quantity">
              </mat-form-field>
              <button mat-raised-button color="accent" (click)="addIngredient(ingredient)">Add</button>
            </mat-list-item>
          </mat-list>
        </div>
      </mat-tab>

      <!-- Selected Ingredients Table -->
      <mat-tab label="Selected Ingredients">
        <div *ngIf="selectedIngredients.length > 0" class="table-container">
          <h3>Selected Ingredients</h3>
          <table mat-table [dataSource]="selectedIngredients" class="mat-elevation-z8">
            <ng-container matColumnDef="ingredientName">
              <th mat-header-cell *matHeaderCellDef> Ingredient Name </th>
              <td mat-cell *matCellDef="let element"> {{element.ingredientName}} </td>
            </ng-container>

            <ng-container matColumnDef="quantity">
              <th mat-header-cell *matHeaderCellDef> Quantity </th>
              <td mat-cell *matCellDef="let element">
                <input matInput type="number" [(ngModel)]="element.quantity">
              </td>
            </ng-container>

            <ng-container matColumnDef="actions">
              <th mat-header-cell *matHeaderCellDef> Actions </th>
              <td mat-cell *matCellDef="let element">
                <button mat-raised-button color="warn" (click)="removeIngredient(element)">Remove</button>
                <button mat-raised-button color="primary">Add to Meal</button>
              </td>
            </ng-container>

            <tr mat-header-row *matHeaderRowDef="['ingredientName', 'quantity', 'actions']"></tr>
            <tr mat-row *matRowDef="let row; columns: ['ingredientName', 'quantity', 'actions']"></tr>
          </table>
        </div>
      </mat-tab>
    </mat-tab-group>
  </div>
</div>
<div class="diet-container">

  <div>
    <button type="button" (click)="showSection(section: 'diet')">Diet</button>
    <button type="button" class="btn btn-secondary" (click)="showSection(section: 'meal')">Meals</button>
    <button type="button" class="btn btn-secondary" (click)="showSection(section: 'ingredient')">Ingredient</button>
  </div>

  <div *ngIf="selectedSection==='diet'">
    <label for="dietName">Diet Name</label>
    <input id="dietName" formControlName="dietName" />
  </div>

  <div *ngIf="selectedSection==='meal'">
    <mat-tab-group>
      <mat-tab label="Create Meal">
        <div class="meal-container" style="margin-left: 428px;">
          <form (ngSubmit)="addMeal()" #mealForm="ngForm">
            <div class="form-group col-lg-12">
              <input [(ngModel)]="mealName" name="mealName" class="form-control" placeholder="Enter the Name of meal" required>
            </div>
            <div class="form-group">
              <input [(ngModel)]="mealDescription" name="mealDescription" class="form-controli" placeholder="Enter the Description" required>
            </div>
            <div class="form-group">
              <input [(ngModel)]="calories" name="calories" class="form-control1" placeholder="Enter Amount of calories" required>
            </div>
            <div class="form-group">
              <input [(ngModel)]="protein" name="protein" class="form-control" placeholder="Enter Amount of protein" required>
            </div>
            <div class="form-group">
              <input [(ngModel)]="fibre" name="fibre" class="form-control1" placeholder="Enter Amount of fiber" required>
            </div>
            <div class="form-group">
              <input [(ngModel)]="fat" name="fat" placeholder="Enter Amount of Fat" required>
            </div>
            <div>
              <button class="btn btn-outline-primary" type="submit" [disabled]="!mealForm.form.valid">Add meal</button>
            </div>
          </form>
        </div>
      </mat-tab>

      <mat-tab label="Add Ingredients">
        <div *ngIf="isMealAdded" class="add-ingredients-container-search">
          <ul class="ingredient-list">
            {{ingredient.ingredientName}}
            <label for="ingredient-search" class="add-ingredients-container-label">Search for an Ingredient</label>
            <input type="text" id="ingredient-search" [(ngModel)]="searchTerm" name="searchTerm" (input)="searchIngredients()" placeholder="Search" class="add-ingredients-container-input">
            <li *ngFor="let ingredient of searchResults">
              {{ingredient.ingredientId}}
              <input type="number" placeholder="Quantity" [ngModel]="quantity"/>
              <button (click)="addIngredient(ingredient)" class="add-ingredients-container-button">Add</button>
            </li>
          </ul>
        </div>
      </mat-tab>

      <mat-tab label="Selected Ingredients">
        <div class="table-container" *ngIf="selectedIngredients.length>0">
          <h3 class="SelectedIn">Selected Ingredient</h3>
          <table>
            <thead>
              <tr>
                <th>Ingredient Name</th>
                <th>Quantity</th>
                <th>Action</th>
                <th>Add to Meal</th>
              </tr>
            </thead>
            <tbody>
              <tr *ngFor="let ingredient of selectedIngredients">
                <td style="color: white">{{ ingredient.ingredientName }}</td>
                <td>
                  <input type="number" [(ngModel)]="ingredient.quantity" name="quantity_{{ingredient.ingredientId}}" placeholder="Quantity">
                </td>
                <td>
                  <button (click)="removeIngredient(ingredient)">Remove</button>
                </td>
                <td>
                  <button class="add-ingredients-container-button" style="background-color:lawngreen">Add</button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </mat-tab>
    </mat-tab-group>
  </div>
</div>



<mat-tab-group (selectedTabChange)="onTabChange($event)">
  <mat-tab label="Create Meal">
    <!-- Create Meal Content Here -->
  </mat-tab>

  <mat-tab label="Display Meal">
    <!-- Display Meal Table -->
    <div *ngIf="meals.length > 0" class="table-container">
      <h3>Available Meals</h3>
      <table class="table table-striped">
        <thead>
          <tr>
            <th>Meal Name</th>
            <th>Calories</th>
            <th>Protein</th>
            <th>Fibre</th>
            <th>Fat</th>
            <th>Ingredients</th>
          </tr>
        </thead>
        <tbody>
          <tr *ngFor="let meal of meals">
            <td>{{ meal.mealName }}</td>
            <td>{{ meal.calories }}</td>
            <td>{{ meal.protein }}</td>
            <td>{{ meal.fibre }}</td>
            <td>{{ meal.fat }}</td>
            <td>
              <ul>
                <li *ngFor="let ingredient of meal.ingredients">{{ ingredient.ingredientName }} - {{ ingredient.quantity }}</li>
              </ul>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </mat-tab>
</mat-tab-group>



  getAllMeals(): Observable<any> {
    return this.http.get(`${this.baseUrl}/meals`);  // Adjust URL according to your backend API
  }

  // Method to load meals when the tab is selected
  loadMeals() {
    this.getAllMeals().subscribe(
      (data: any[]) => {
        this.meals = data;
        console.log('Meals fetched:', this.meals);
      },
      (error) => {
        console.error('Error fetching meals:', error);
      }
    );
  }


  onTabChange(event: any): void {
  // Check if the Display Meal tab is selected
  if (event.index === 1) {
    this.loadMeals();
  }
}


<div>
  <form [formGroup]="dietForm" (ngSubmit)="onSubmit()">
    <label for="dietName">Diet Name</label>
    <input id="dietName" formControlName="dietName" />

    <label for="dietType">Diet Type</label>
    <input id="dietType" formControlName="dietType" />

    <label for="dietDescription">Diet Description</label>
    <textarea id="dietDescription" formControlName="dietDescription"></textarea>

    <label for="dietMeal">Search Meals</label>
    <input type="text" (input)="onMealSearch($event.target.value)" placeholder="Search meals by name" />
    <div *ngIf="filteredMeals.length > 0">
      <ul>
        <li *ngFor="let meal of filteredMeals" (click)="addMealToDiet(meal)">
          {{ meal.mealName }}
        </li>
      </ul>
    </div>

    <button type="submit">Create Diet</button>
  </form>

  <!-- Table to display diets -->
  <div>
    <table>
      <thead>
        <tr>
          <th>Diet Name</th>
          <th>Meal Name</th>
          <th>Ingredient Name</th>
        </tr>
      </thead>
      <tbody>
        <tr *ngFor="let diet of diets">
          <td>{{ diet.dietName }}</td>
          <td>{{ diet.meals.map(m => m.mealName).join(', ') }}</td>
          <td>{{ diet.meals.map(m => m.ingredients.map(i => i.name).join(', ')).join(', ') }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>


<div *ngIf="selectedSection==='diet'">
  <div class="row" style="margin-top: 6vh">
    
    <!-- Display meals -->
    <div class="col-sm-6">
      <ul class="Meal-list">
        <li class="row" *ngFor="let meal of searchMealResult">
          <div class="col-md-4">{{ meal.mealName }}</div>
          <div class="col-md-4">{{ meal.ingredients }}</div>
          <div>
            <button (click)="selectMeal(meal)"> ADD Meal</button>
          </div>
        </li>
      </ul>
    </div>

    <!-- Display diets in a table -->
    <div class="col-sm-6">
      <table class="table">
        <thead>
          <tr>
            <th>Diet Name</th>
            <th>Diet Type</th>
            <th>Diet Description</th>
            <th>Meals</th>
          </tr>
        </thead>
        <tbody>
          <tr *ngFor="let diet of diets">
            <td>{{ diet.dietName }}</td>
            <td>{{ diet.dietType }}</td>
            <td>{{ diet.dietDescription }}</td>
            <td>
              <ul>
                <li *ngFor="let meal of diet.dietMeals">
                  {{ meal.mealName }}
                </li>
              </ul>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    
  </div>
</div>
