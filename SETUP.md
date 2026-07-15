# Deploying your new profile

## 1. The special repo
Your profile README lives in a repo named exactly after your username:
`github.com/<your-username>/<your-username>`. Create it (public) if it doesn't exist,
then drop `README.md` and the `assets/` folder in the root.

## 2. Two placeholders to fix
- In `README.md`, replace `YOUR_GITHUB_USERNAME` in the activity-graph URL with your actual GitHub username.
- The README references `assets/rag-demo.gif` — create it in step 3.

## 3. Make the RAG demo GIF
Download your existing demo mp4, pick the best ~10 seconds (retrieval + answer appearing), then:

```bash
ffmpeg -i demo.mp4 -ss 00:00:05 -t 10 \
  -vf "fps=12,scale=900:-1:flags=lanczos,split[s0][s1];[s0]palettegen=max_colors=128[p];[s1][p]paletteuse=dither=bayer" \
  assets/rag-demo.gif
```

- Adjust `-ss` (start time) and `-t` (duration) to your best segment.
- Keep the file under ~10 MB or GitHub won't render it inline. If it's over:
  drop `fps=12` to `fps=10`, or `scale=900` to `scale=760`, or shorten `-t`.

## 4. Commit
```bash
git add README.md assets/
git commit -m "profile v2: blueprint cards + ascii architecture"
git push
```

## 5. Verify
- Check the profile in both dark and light GitHub themes (hero swaps automatically).
- Check on your phone — ASCII blocks scroll horizontally, that's expected and fine.
- The animated banner/cards animate in the browser; GitHub's mobile app sometimes shows them static. Known limitation, nothing to fix.

## Editing later
The SVGs are plain hand-written XML — open in any editor. Metrics live in obvious
`<text>` elements. If you want a new card for a future project, copy `card-iiot.svg`
and change the boxes; the geometry is a simple 1100×300 grid.
