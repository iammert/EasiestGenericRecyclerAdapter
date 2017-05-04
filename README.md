# Easiest Generic Recycler Adapter

Easiest way to use RecyclerView. Reduce boilerplate code!
### How to Use:

Create your ViewHolder class & extend GenericViewHolder 

```java
public class YourViewHolder extends GenericViewHolder {
	private YourModel mItem;

    public StoryViewHolder(final View itemView) {
        super(itemView);
    }

    public void bindData(final Object element) {
        mItem = (YourModel) element;
        // set whatever you want. for instance;
        surname.setText(mItem.getSurname());
        name.setText(mItem.getName());
    }
}
```
In your activity/fragment class you can initialize adapter like below;
```java
GenericRecyclerAdapter mAdapter;
RecyclerView mMainRv;
```
```java
    mMainRv.setLayoutManager(new LinearLayoutManager(this)); //set your layout manager
    mAdapter = new GenericAdapterBuilder().addModel(
    			R.layout.contact_row, //set your row's layout file
                YourViewHolder.class, //set your view holder class
                YourModel.class) // set your model class(If you use just String list, it can be just String.class)
                .execute();
        mMainRv.setAdapter(mAdapter);
    }
```
If your list contains different row types, you can add them like below;
```java
   mAdapter = new GenericAdapterBuilder().addModel(
    			R.layout.contact_row, 
                YourViewHolder.class, 
                YourModel.class).addModel(
    			R.layout.second_row, 
                YourSecondViewHolder.class, 
                YourSecondModel.class).addModel(
    			R.layout.third_row, 
                YourThirdViewHolder.class, 
                YourThirdModel.class)
                .execute(); 
    }
```
Now you are ready to set your list!

Assume that you have 
```java
List<YourModel> mList
```
or
```java
List<Object> mList
```
then you can set your list like this.
```java
mAdapter.addItem(mList);
```
if you want to add new row(s) to your existing list, you can use addNewRows method. You can pass any object or object list to addNewRows method like below;
```java
mAdapter.addNewRows(mList);
```
or
```java
YourModel lItem;
mAdapter.addNewRows(lItem);
```

You can use getItems method to get current items of the list;

```java
List<YourModel> mList = mAdapter.getItems();
```

### Using with Filter
If you want to use filter with your recyclerview for searching etc. you have to enable & set filter like below;
```java
mAdapter = new GenericAdapterBuilder().addModel(
    			R.layout.contact_row, 
                YourViewHolder.class, 
                YourModel.class)
                .setFilterEnabled() //important!
                .execute();
                
 mAdapter.setFilter(new SearchFilter(mAdapter));
```

Example search filter class;

```java
public class SearchFilter extends Filter {

   private GenericRecyclerAdapter mAdapter;

   public SearchFilter(GenericRecyclerAdapter pAdapter) {
      mAdapter = pAdapter;
   }

   @Override
   protected FilterResults performFiltering(CharSequence constraint) {
     
      FilterResults results = new FilterResults();

      final List<Object> list = mAdapter.mOriginalObjectList;

      final ArrayList<Object> filteredList = new ArrayList<>();

      String filterableString = "";

      for (Object lModel : list) {
         if (lModel instanceof YourModel) {
            filterableString = ((YourModel) lModel).getValue();
         }  
         if (filterableString.contains(constraint.toString())) {
            filteredList.add(lModel);
         }
      }

      results.values = filteredList;
      results.count = filteredList.size();

      return results;
   }

   @Override
   protected void publishResults(CharSequence constraint, FilterResults results) {
      mAdapter.mObjectList = (List<Object>) results.values;
      mAdapter.notifyDataSetChanged();
   }
}
```
you can set & listen the result in your edittext's afterTextChanged method;
```java
mAdapter.getFilter().filter(editable.toString(), new Filter.FilterListener() {
                  @Override
                  public void onFilterComplete(int pI) {
                     if (mAdapter.mObjectList == null || mAdapter.mObjectList.size() <= 0) 					{//If the search key is deleted, set the original list
                       mAdapter.addItem(mAdapter.mOriginalObjectList);
                     }
                  }
               });
   ```
