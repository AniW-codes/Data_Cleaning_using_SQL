# Data Cleaning using SQL

## Project Overview

**Project Title**: Nashville Housing Data for Data Cleaning  

**Level**: Beginner

**Database**: `Portfolio_Project`

This project demonstrates the implementation of data cleaning using SQL. It includes cleaning and  managing tables, performing CRUD operations, and executing advanced SQL queries. 

## Project Structure

- **Database Creation**: Created a database named `Portfolio_Project`.

--Standardizing 'SaleDate'

```sql
Select SaleDate, CONVERT(Date,SaleDate)
from Portfolio_Project.dbo.NashvilleHousing

```

--Adding column in table

```sql
Alter Table Portfolio_Project.dbo.NashvilleHousing
Add SaleDate_Fixed Date

Update Portfolio_Project.dbo.NashvilleHousing
Set SaleDate_Fixed = Convert(Date, SaleDate)

Select * 
from Portfolio_Project.dbo.NashvilleHousing


```


--- Ensuring data in 'PropertyAddress' field

```sql
Select *
from Portfolio_Project.dbo.NashvilleHousing
where PropertyAddress is null

```

--Code to filterout data with PropertyAddress as Null and identify common data for same ParcelID to PropertyAddress

```sql

Select a.ParcelID,
            a.PropertyAddress,
            b.ParcelID,
            b.PropertyAddress,
            ISNULL(a.PropertyAddress, b.PropertyAddress)
from Portfolio_Project.dbo.NashvilleHousing a
join Portfolio_Project.dbo.NashvilleHousing b
on a.ParcelID = b.ParcelID and a.[UniqueID ] != b.[UniqueID ]
where a.PropertyAddress is null


Update a
Set PropertyAddress = ISNULL(a.PropertyAddress, b.PropertyAddress)
from Portfolio_Project.dbo.NashvilleHousing a
join Portfolio_Project.dbo.NashvilleHousing b
on	a.ParcelID = b.ParcelID
and a.[UniqueID ] != b.[UniqueID ]
where a.PropertyAddress is null

```



--- Segregaring Property Address Column (Delimiter - Difficult)

```sql
Select *
from Portfolio_Project.dbo.NashvilleHousing


Select
SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress)-1) as Address, --CharIndex provides position of the comma in the form of integer
SUBSTRING(PropertyAddress, CHARINDEX(',',PropertyAddress)+1,LEN(PropertyAddress)) as City
from Portfolio_Project.dbo.NashvilleHousing

Alter Table Portfolio_Project.dbo.NashvilleHousing
Add PropertySplit_Address nvarchar(255)

Update Portfolio_Project.dbo.NashvilleHousing
Set PropertySplit_Address = SUBSTRING(PropertyAddress,1,CHARINDEX(',',PropertyAddress)-1) 

Alter Table Portfolio_Project.dbo.NashvilleHousing
Add PropertySplit_City nvarchar(255)

Update Portfolio_Project.dbo.NashvilleHousing
Set PropertySplit_City = SUBSTRING(PropertyAddress, CHARINDEX(',',PropertyAddress)+1,LEN(PropertyAddress)) 

Select *
from Portfolio_Project.dbo.NashvilleHousing

```


--Using ParseName to segregate OwnerAddress (Easier)


```sql
Select PARSENAME(REPLACE(OwnerAddress,',','.'),3) as OwnerAddress,
PARSENAME(REPLACE(OwnerAddress,',','.'),2) as OwnerCity,
PARSENAME(REPLACE(OwnerAddress,',','.'),1) as OwnerState
from Portfolio_Project.dbo.NashvilleHousing

Alter Table Portfolio_Project.dbo.NashvilleHousing
Add OwnerSplit_Address nvarchar (255)

Alter Table Portfolio_Project.dbo.NashvilleHousing
Add OwnerSplit_City nvarchar (255)

Alter Table Portfolio_Project.dbo.NashvilleHousing
Add OwnerSplit_State nvarchar (255)

Update Portfolio_Project.dbo.NashvilleHousing
Set OwnerSplit_Address = PARSENAME(REPLACE(OwnerAddress,',','.'),3)

Update Portfolio_Project.dbo.NashvilleHousing
Set OwnerSplit_City = PARSENAME(REPLACE(OwnerAddress,',','.'),2)

Update Portfolio_Project.dbo.NashvilleHousing
Set OwnerSplit_State = PARSENAME(REPLACE(OwnerAddress,',','.'),1)


Select *
from Portfolio_Project.dbo.NashvilleHousing


```



---Changing Y/N to Yes or No on 'SoldAsVaccant' Column


```sql
Select Distinct(SoldAsVacant), COUNT(SoldAsVacant)
from Portfolio_Project.dbo.NashvilleHousing
Group By SoldAsVacant

Select SoldAsVacant,
CASE When SoldAsVacant = 'Y' THEN 'Yes'
	When SoldAsVacant = 'N' THEN 'No'
	Else SoldAsVacant
	END
from Portfolio_Project.dbo.NashvilleHousing

Update Portfolio_Project.dbo.NashvilleHousing
Set SoldAsVacant = CASE When SoldAsVacant = 'Y' THEN 'Yes'
	When SoldAsVacant = 'N' THEN 'No'
	Else SoldAsVacant
	END


Select * from Portfolio_Project.dbo.NashvilleHousing

```


---Removing Duplicates by using Row Number as CTE and windows function of Partition By


```sql
WITH RowNumCTE AS(
Select *,
	ROW_NUMBER() OVER (
	PARTITION BY ParcelID,
				 PropertyAddress,
				 SalePrice,
				 SaleDate,
				 LegalReference
				 ORDER BY
					UniqueID
					) row_num

From Portfolio_Project.dbo.NashvilleHousing
--order by ParcelID
)
Select *
From RowNumCTE
Where row_num > 1
--Order by PropertyAddress



Select *
From Portfolio_Project.dbo.NashvilleHousing



```


---Delete unused columns


```sql
Alter Table Portfolio_Project.dbo.NashvilleHousing
DROP COLUMN SaleDate

Alter Table Portfolio_Project.dbo.NashvilleHousing
DROP COLUMN OwnerAddress, TaxDistrict, PropertyAddress


Select *
From Portfolio_Project.dbo.NashvilleHousing
```

## Author - Aniruddha

This project showcases SQL skills for data cleaning purposes which are essential for database management and analysis. For more content on SQL and data analysis, connect with me through the following channels:

- **LinkedIn**: [Connect with me professionally](https://www.linkedin.com/in/aniruddhawarang/)

Thank you for your interest in this project!
