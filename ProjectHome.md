Adds new features to Android's ListView widget:
  * section headers (e.g. the one used in built-in Contacts applications showing the list of names)
  * item pagination (loading of next page of items) (e.g. the list of applications on Android Market, the list of emails on Gmail app).

| ![https://lh6.googleusercontent.com/_ODdyLCCXPpQ/TZwrwdeUtwI/AAAAAAAAw04/2CNHOfSQIYs/s400/device.png](https://lh6.googleusercontent.com/_ODdyLCCXPpQ/TZwrwdeUtwI/AAAAAAAAw04/2CNHOfSQIYs/s400/device.png) | ![https://lh4.googleusercontent.com/_ODdyLCCXPpQ/TZwr-_i8BHI/AAAAAAAAw08/FVBKIBKuCLM/s400/device2.png](https://lh4.googleusercontent.com/_ODdyLCCXPpQ/TZwr-_i8BHI/AAAAAAAAw08/FVBKIBKuCLM/s400/device2.png) |
|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Section headers                                                                                                                                                                                           | Loading next page of items                                                                                                                                                                                  |

This widget is used in the [Foound](http://www.foound.com) application.


# How to use #

Tip: You may also want to consult the [sample project](http://code.google.com/p/android-amazing-listview/source/browse/#svn%2Ftrunk%2FAmazingListViewDemo).

## Preparation ##

### For section headers ###
You will need a special kind of view for your items, which as a header on _every_ item. This is one of the items you need. It consist of 2 parts, the blue upper parts (called "item header" from now on) and the white lower part (called "item content" from now on):

| ![https://lh4.googleusercontent.com/_ODdyLCCXPpQ/TZwsaYHKDhI/AAAAAAAAw1A/k4t1r7RA2dY/s800/Screen%20shot%202011-04-06%20at%205.03.10%20PM.png](https://lh4.googleusercontent.com/_ODdyLCCXPpQ/TZwsaYHKDhI/AAAAAAAAw1A/k4t1r7RA2dY/s800/Screen%20shot%202011-04-06%20at%205.03.10%20PM.png) |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

Hiding and showing of the item header will be done in `bindSectionHeader` (see below).

Then, you will need another view, "pinned header" (which is effectively the blue part above):

| ![https://lh4.googleusercontent.com/_ODdyLCCXPpQ/TZwsobgS1mI/AAAAAAAAw1E/dQsV3WXaqqk/s800/Screen%20shot%202011-04-06%20at%205.04.10%20PM.png](https://lh4.googleusercontent.com/_ODdyLCCXPpQ/TZwsobgS1mI/AAAAAAAAw1E/dQsV3WXaqqk/s800/Screen%20shot%202011-04-06%20at%205.04.10%20PM.png) |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

This view need to be set up using `setPinnedHeaderView` (see below).

You may want to use `<include>` the pinned header into the list item as item header to ensure consistency.

### For pagination ###
You need a view that says the loading message. Set this via `setLoadingView`. Here is an example.

| ![https://lh5.googleusercontent.com/_ODdyLCCXPpQ/TZGX8fzOtVI/AAAAAAAAwyI/CWFfuEDSFL4/s320/Screen%20shot%202011-03-29%20at%204.27.09%20PM.png](https://lh5.googleusercontent.com/_ODdyLCCXPpQ/TZGX8fzOtVI/AAAAAAAAwyI/CWFfuEDSFL4/s320/Screen%20shot%202011-03-29%20at%204.27.09%20PM.png) |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

## Basic setup ##

Create your adapter (data/view provider) by deriving `AmazingAdapter`. There are many methods that you _need_ to implement, but if you don't use either section headers or pagination, you can leave them as "almost" empty methods.

What you definitely need to implement:
  * `getCount` See [Adapter's documentation](http://developer.android.com/reference/android/widget/Adapter.html#getCount()).
  * `getItem` See [Adapter's documentation](http://developer.android.com/reference/android/widget/Adapter.html#getItem(int)).
  * `getItemId` See [Adapter's documentation](http://developer.android.com/reference/android/widget/Adapter.html#getItemId(int)).
  * `getAmazingView` Implement the code to return the view containing the item content and the item header here instead of at `getView`. See [Adapter's documentation](http://developer.android.com/reference/android/widget/Adapter.html#getView(int,%20android.view.View,%20android.view.ViewGroup)). If you want to override `getView`, make sure you call super implementation.

## For section headers ##

If you want to have sections, implement these methods (otherwise, just `return`, `return 0`, or `return null`):
  * `getPositionForSection` Returns the first position of a given section number (starts at 0). See [SectionIndexer's documentation](http://developer.android.com/reference/android/widget/SectionIndexer.html#getPositionForSection(int)).
  * `getSectionForPosition` Returns the section number for a position. See [SectionIndexer's documentation](http://developer.android.com/reference/android/widget/SectionIndexer.html#getSectionForPosition(int)).
  * `getSections` Returns an array of the data for the section headers. See [SectionIndexer's documentation](http://developer.android.com/reference/android/widget/SectionIndexer.html#getSections()).
  * `bindSectionHeader` Similar to `getAmazingView`, but this is for _item headers_. When this is called, you will be given 3 items: the view _to modify_, a position of the item, and a boolean `displaySectionHeaders`. You need to _hide_ (set to GONE) the item header from the view if `displaySectionHeaders` is false, and _show_ (set to VISIBLE) otherwise. Unlike `getAmazingView`, this method doesn't return anything, so your task is _to modify_ the view given.
  * `configurePinnedHeader` This controls how the _pinned header_ looks. The pinned header is the header on top of the screen that doesn't scroll together with the list until another section header takes place. Normally you want to set the text of your header according to the position given (and hence the section), set the opacity according to the alpha given.

## For pagination ##

If you want to have pagination, implement these methods (otherwise, just `return`):
  * `onNextPageRequested` Called when user scrolls the list until the "loading" indicator is visible. This is called on UI thread, so if you have some network requests, make sure you do it on the background. The page number is also given.

By default, you are considered to be in the last page, and you will not see any "Loading..." message on the bottom of the list. You need to control whether there are still pages to be displayed.
  * `notifyMayHaveMorePages` to indicate that we still have more data, hence "Loading..." message will be shown.
  * `notifyNoMorePages` to indicate that we have loaded all the data, hence no more "Loading..." message to be shown.
  * `nextPage` to increase the page number. Do this when you append new data into your adapter.
  * `resetPage` to reset the page number to 1 (or to the value specified by `setInitialPage`).

## Set up the AmazingListView ##

  * Create or inflate an instance of AmazingListView.
  * If using section headers, call `setPinnedHeaderView` with the view you use as the pinned header. **You need to set the root to the AmazingListView instance** (e.g. use `LayoutInflater#inflate(R.layout.your_header, amazingListView, false)`).
  * If using pagination,  call `setLoadingView` on initialization to set the view for the "Loading..." message (with some fun spinner etc.)
  * Call `setAdapter` with the adapter created above.

## Credits ##
  * ListView with section headers was adapted from [Google I/O Schedule App for Android](http://code.google.com/p/iosched/)