# Multimodal Food Recommendation System

A multimodal food recommendation system that combines food images and textual food information to retrieve food recommendations based on a user's natural-language craving or preference.

The system uses a personal food dataset consisting of food images and an Excel file containing food descriptions and ingredients. It performs independent image and text retrieval, combines their results through late fusion, and ranks the matched food items using semantic similarity.

## Features

- Multimodal food retrieval using image and text modalities
- Image-based search using OpenCLIP embeddings
- Text-based search using Sentence Transformers
- Vector database storage using ChromaDB
- Late fusion to identify food items matching both modalities
- Cosine similarity ranking for the final matched results
- Displays recommended food images, descriptions, and ingredients

## System Overview

The recommendation pipeline consists of the following stages:

```text
User Food Craving
       │
       ▼
 ┌───────────────┐
 │  User Query   │
 └───────┬───────┘
         │
    ┌────┴────┐
    ▼         ▼
 Image       Text
 Search      Search
    │         │
    ▼         ▼
  CLIP     Sentence
 Embedding Transformer
    │         │
    ▼         ▼
ChromaDB   ChromaDB
 Image DB   Text DB
    │         │
    └────┬────┘
         ▼
    Late Fusion
   (Intersection)
         │
         ▼
 Cosine Similarity
      Ranking
         │
         ▼
 Food Recommendations
```
## Dataset

This project uses a personal food dataset collected by the author. The dataset is included in this repository and consists of food images and an Excel file containing textual information about each menu item.

### Dataset Components

#### Food Images

Food images are stored in the `data/menu_image/` directory.

```text
data/
└── menu_image/
    ├── food_01.jpg
    ├── food_02.jpg
    ├── food_03.jpg
    └── ...
```

#### Menu Descriptions

Food-related textual information is stored in:

```text
data/menu_description.xlsx
```

The Excel file contains information associated with each food image, including:

| Column        | Description                              |
| ------------- | ---------------------------------------- |
| `filename`    | Filename of the corresponding food image |
| `description` | Description of the food                  |
| `ingredients` | Ingredients contained in the food        |

The `filename` column is used to link each food image with its corresponding description and ingredient information.

### Dataset Structure

```text
data/
├── menu_image/
│   ├── food_01.jpg
│   ├── food_02.jpg
│   ├── food_03.jpg
│   └── ...
│
└── menu_description.xlsx
```

> **Note:** The dataset is a personal dataset collected by the author and is provided in this repository for the purpose of demonstrating the multimodal food recommendation system.

## Models

### Image Embedding

The system uses OpenCLIP to generate embeddings for food images and perform image-based semantic retrieval.

```python
clip_embedder = OpenCLIPEmbeddingFunction()
```

### Text Embedding

Textual food information is embedded using:

```text
all-MiniLM-L6-v2
```

The text representation combines the food description and ingredients:

```text
Description. Ingredients: ingredient list
```

This allows the system to retrieve foods based on semantic similarity between the user's query and the available food information.

## Vector Databases

Two persistent ChromaDB databases are used:

```text
/data/image_db
/data/text_db
```

The image database stores:

- Image URI
- Filename
- Food description
- Ingredients
- CLIP embeddings

The text database stores:

- Filename
- Food description
- Ingredients
- Sentence Transformer embeddings

## Recommendation Process

The user provides a natural-language food preference, for example:

```text
sweet
```

or:

```text
spicy with tofu
```

The query is processed through two independent retrieval pipelines.

### 1. Image Retrieval

The query is sent to the image database using the OpenCLIP embedding function.

The system retrieves the top `K` visually/semantically relevant food images.

### 2. Text Retrieval

The same query is converted into a text embedding using `all-MiniLM-L6-v2`.

The embedding is then compared against the food descriptions and ingredients stored in the text database.

### 3. Late Fusion

The results from the two modalities are combined using an intersection operation.

Only food items appearing in both the image-based and text-based retrieval results are considered multimodal matches.

```python
intersect_files = img_files & txt_files
```

### 4. Similarity Ranking

The matched food items are further ranked using cosine similarity between:

- The user's query
- The food description + ingredients

The system then presents the matched food items along with their images, descriptions, and ingredients.

## Example

Input:

```text
spicy with tofu
```

The system searches both modalities and identifies food items that are relevant to the query in:

1. Image-based retrieval
2. Description/ingredient-based retrieval

The overlapping results are then ranked according to their semantic similarity.

Example output:

```text
--- food_01.jpg ---

Description: Spicy tofu dish with vegetables
Ingredients: tofu, chili, vegetables, garlic

Similarity: 0.8421
```

## Project Structure

```text
multimodal-food-recommendation/
│
├── data/
│   ├── menu_image/
│   │   ├── food_01.jpg
│   │   ├── food_02.jpg
│   │   └── ...
│   │
│   └── menu_description.xlsx
│
├── main.ipynb
├── requirements.txt
└── README.md
```

The generated ChromaDB directories can be created during execution:

```text
/data/
├── image_db/
└── text_db/
```

## Running the Project

Open the notebook:

```bash
jupyter notebook main.ipynb
```

Or run it using Google Colab.

For local execution, the dataset paths may need to be adjusted accordingly.

Run the cells and enter a food craving when prompted:

```text
Enter your food craving (e.g., sweet, spicy with tofu):
```
