# docx

a ruby library/gem for interacting with `.docx` files. currently capabilities include reading paragraphs/bookmarks, inserting text at bookmarks, reading tables/rows/columns/cells and saving the document.

## usage

### install

requires ruby (only tested with 1.9.3 so far)

    gem install docx

### reading

``` ruby
require 'docx'

# Create a Docx::Document object for our existing docx file
doc = Docx::Document.open('example.docx')

# Retrieve and display paragraphs
doc.paragraphs.each do |p|
  puts p
end

# Retrieve and display bookmarks, returned as hash with bookmark names as keys and objects as values
doc.bookmarks.each_pair do |bookmark_name, bookmark_object|
  puts bookmark_name
end
```

### reading tables

``` ruby
require 'docx'

# Create a Docx::Document object for our existing docx file
doc = Docx::Document.open('tables.docx')

first_table = doc.tables[0]
puts first_table.row_count
puts first_table.column_count
puts first_table.rows[0].cells[0].text
puts first_table.columns[0].cells[0].text

# Iterate through tables
doc.tables.each do |table|
  table.rows.each do |row| # Row-based iteration
    row.cells.each do |cell|
      puts cell.text
    end
  end
  
  table.columns.each do |column| # Column-based iteration
    column.cells.each do |cell|
      puts cell.text
    end
  end
end
```

### writing

``` ruby
require 'docx'

# Create a Docx::Document object for our existing docx file
doc = Docx::Document.open('example.docx')

# Insert a single line of text after one of our bookmarks
doc.bookmarks['example_bookmark'].insert_after("Hello world.")

# Insert multiple lines of text at our bookmark
doc.bookmarks['example_bookmark_2'].insert_multiple_lines_after(['Hello', 'World', 'foo'])

# Save document to specified path
doc.save('example-edited.docx')
```

### advanced

``` ruby
require 'docx'

d = Docx::Document.open('example.docx')

# The Nokogiri::XML::Node on which an element is based can be accessed using #node
d.paragraphs.each do |p|
  puts p.node.inspect
end

# The #xpath and #at_xpath methods are delegated to the node from the element, saving a step
p_element = d.paragraphs.first
p_children = p_element.xpath("//child::*") # selects all children
p_child = p_element.at_xpath("//child::*") # selects first child
```

## Development

### todo

* Add better formatting identification for specific nodes and other formatting indicators (text size, paragraph spacing)
* Calculate element formatting based on values present in element properties as well as properties inherited from parents
* Default formatting of inserted elements to inherited values
* Implement formattable elements.
* Implement styles.
* Easier multi-line text insertion at a single bookmark (inserting paragraph nodes after the one containing the bookmark)