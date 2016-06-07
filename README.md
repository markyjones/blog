*Build*

hugo --theme=hugo-minimalist-theme
cp -r ../public/ .
git add -A
git push origin gh-pages
