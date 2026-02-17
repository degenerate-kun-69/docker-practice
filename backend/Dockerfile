FROM python:3.14.2-slim AS builder
#separate the build stage from run stage using stage alias
WORKDIR /app

# single stage build 
# COPY requirements.txt requirements.txt
# for multi stage build copy toml and requirements

COPY pyproject.toml requirements.txt ./
# pip install to be avoided, instead fetch pre-built wheels for installation
# RUN pip install --no-cache-dir -r requirements.txt
# this creates pre-built installable wheels for project {GLORIFIED ZIP FILES}
RUN pip wheel --no-cache-dir --no-deps --wheel-dir wheels -r requirements.txt 

# copy source code and build a wheel for it as well
COPY src src
RUN pip wheel --no-cache-dir --no-deps --wheel-dir wheels .
# COPY src/mysite mysite

# running code
FROM python:3.14.2-slim AS runner

# --from flag is used to tell docker source location isn't from build context,
# it's from the prev builder stage
COPY --from=builder /app/wheels /wheels
# grouping 2 commands into a single run {&&rm -rf} creates 1 less layer so more optimization 
RUN pip install --no-cache /wheels/* && rm -rf /wheels
 
EXPOSE 8000

CMD ["uvicorn","mysite.main:app","--host","0.0.0.0","--port","8000"]
