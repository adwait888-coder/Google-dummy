# Copyright 2020 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM node:20-alpine AS builder

# 1. आवश्यक बिल्ड टूल्स आणि डाऊनलोडसाठी curl, tar इन्स्टॉल करा
RUN apk add --update --no-cache \
    python3 \
    make \
    g++ \
    curl \
    tar

WORKDIR /usr/src/app

# 2. सध्याच्या Node version नुसार हेडर्स फाईल मॅन्युअली डाउनलोड करून एक्सट्रॅक्ट करा
# यामुळे node-gyp ला इंटरनेटची गरज पडणार नाही
RUN NODE_VERSION=$(node -v | tr -d 'v') && \
    mkdir -p /tmp/node-headers && \
    curl -sL https://nodejs.org/download/release/v$NODE_VERSION/node-v$NODE_VERSION-headers.tar.gz | tar -xz -C /tmp/node-headers --strip-components=1

# 3. node-gyp साठी लोकल हेडर्सचा पाथ सेट करा
ENV npm_config_nodedir=/tmp/node-headers

COPY package*.json ./

# 4. आता npm install केल्यावर pprof आरामात कंपाइल होईल
RUN npm install --only=production

FROM node:20-alpine

WORKDIR /usr/src/app

COPY --from=builder /usr/src/app/node_modules ./node_modules

COPY . .

EXPOSE 7000

ENTRYPOINT [ "node", "server.js" ]
