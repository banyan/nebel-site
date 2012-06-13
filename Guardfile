guard 'shell' do
  watch(/layouts\/(.+)/) { `nebel -b ''` }
  watch(/static\/(.+)/)  { `nebel -b ''` }
  watch(/posts\/(.+)/)   { `nebel -b ''` }
  watch(/plugins\/(.+)/) { `nebel -b ''` }
end

guard 'livereload' do
  watch(%r{layouts/(.+)})
  watch(%r{static/(.+)})
  watch(%r{posts/(.+\.md$)})
  watch(%r{posts/(.+\.markdown$)})
  watch(%r{plugins/(.+)})
end
