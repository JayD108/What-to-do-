"# blahblahhhh" 
planning to build something but the question is "WHAT TO DO"?
sequenceDiagram
    autonumber
    participant B as Browser (usePipeline)
    participant A as FastAPI
    participant J as Job folder

    B->>A: POST /api/upload (multipart MP4)
    A->>J: create jobs/{uuid}/input.mp4
    A-->>B: video_id

    loop extract-audio, separate, transcribe, translate, synthesize, merge
        B->>A: POST /api/{step} with video_id and options
        A->>J: delete this step's output and every later output
        A->>J: run step, write its output
        A-->>B: 200 with the result, or 400/500 with an error detail
    end

    opt Edit the translation
        B->>A: PUT /api/translation with the edited sentences
        A->>J: save translation.json, delete voice, subtitles and video
        Note over B,A: then synthesize and merge run again
    end

    B->>A: GET /api/download/{uuid}
    A-->>B: output.mp4