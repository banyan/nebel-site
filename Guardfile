guard 'shell' do
  cmd = "nebel -b '' --no-clean-dir"
  watch(/layouts\/(.+)/) { `#{cmd}` }
  watch(/static\/(.+)/)  { `#{cmd}` }
  watch(/posts\/(.+)/)   { `#{cmd}` }
  watch(/plugins\/(.+)/) { `#{cmd}` }
end

guard 'livereload' do
  watch(%r{layouts/(.+)})
  watch(%r{static/(.+)})
  watch(%r{posts/(.+\.md$)})
  watch(%r{posts/(.+\.markdown$)})
  watch(%r{plugins/(.+)})
end
