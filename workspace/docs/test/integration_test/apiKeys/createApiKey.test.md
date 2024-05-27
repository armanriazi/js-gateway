import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminUsername,
  adminPassword,
  username,
  password,
  mockExpireDate,
  mockMaxCount,
  mockPassword,
  mockUsername2,
  mockRole,
} from '../data/apikey.js'
import apiKeyService from '../requiredTest/apiKeyService.js'
import messages from '../../../src/common/messages.js'
import usersService from '../requiredTest/userService.js'

export const apiKeyCreate = () => {
  describe('CREATE APIKEY', () => {
    let apiKeyId
    let token
    let userToken
    let apiKey
    let userId
    let adminToken
    let adminId
    let superAdminId
    let apiKeyId2
    let apiKeyId3
    let adminId2

    beforeAll(async () => {
      const superAdminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin'
      }

      superAdminId = await usersService.create(superAdminInfo)

      delete superAdminInfo.role

      token = await usersService.login(superAdminInfo)

      const adminInfo = {
        username: randomUUID(),
        password: adminPassword,
        role: 'superAdmin'
      }

      adminId = await usersService.create(adminInfo)

      delete adminInfo.role

      adminToken = await usersService.login(adminInfo)

      const adminInfo2 = {
        username: randomUUID(),
        password: adminPassword,
        role: 'admin'
      }

      adminId2 = await usersService.create(adminInfo2)

      const userInfo = {
        username: randomUUID(),
        password: password,
        role: 'user'
      }

      userId = await usersService.create(userInfo)

      delete userInfo.role

      userToken = await usersService.login(userInfo)
    })

    afterAll(async () => {
      await apiKeyService.delete(apiKeyId)
      await apiKeyService.delete(apiKeyId2)
      await apiKeyService.delete(apiKeyId3)
      await usersService.delete(superAdminId)
      await usersService.delete(userId)
      await usersService.delete(adminId)
    })

    beforeEach(() => {
      apiKey = {
        UserId: userId,
        maxCount: mockMaxCount,
        expireDate: mockExpireDate,
      }
    })

    it('Registration completed successfully', (done) => {
      try {
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            apiKeyId = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: apiKeyId,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('UserId is required', (done) => {
      try {
        delete apiKey.UserId

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.USER_ID} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate format is incorrect', (done) => {
      try {
        apiKey.expireDate = '2022-12-20T05:50:34.823Z'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.EXPIRE_DATE_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate must be in hour(h) or day(d) format', (done) => {
      try {
        apiKey.expireDate = '1w'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.EXPIRE_DATE_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate must be started with numbers', (done) => {
      try {
        apiKey.expireDate = 'oneh'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.EXPIRE_DATE_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('between number and the word in expireDate should not be any character, not even a space', (done) => {
      try {
        apiKey.expireDate = '1 h'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.EXPIRE_DATE_FORMAT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate should be string', (done) => {
      try {
        apiKey.expireDate = 234523423424

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EXPIRE_DATE} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate should not be empty', (done) => {
      try {
        apiKey.expireDate = ''
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EXPIRE_DATE} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('userId should not be empty', (done) => {
      try {
        apiKey.UserId = ''
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.USER_ID} ${messages.m.EMPTY}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('userId must be string', (done) => {
      try {
        apiKey.UserId = 3452345345345345
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.USER_ID} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not create an apiKey', (done) => {
      try {
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${userToken}`)
          .send(apiKey)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can create apiKeys for users', (done) => {
      try {
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(apiKey)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            apiKeyId2 = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: apiKeyId2,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can create apiKeys for themselves', (done) => {
      try {
        apiKey.UserId = adminId

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${adminToken}`)
          .send(apiKey)
          .expect(201)

          .end(function (err, res) {
            if (err) return done(err)

            apiKeyId3 = res.body.data.message.id

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: expect.objectContaining({
                  id: apiKeyId3,
                }),
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not create an apiKey for other admins', (done) => {
      try {
        apiKey.UserId = adminId2
        
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${userToken}`)
          .send(apiKey)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('admins can not create an apiKey for superAdmins', (done) => {
      try {
        apiKey.UserId = superAdminId

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${userToken}`)
          .send(apiKey)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('expireDate is required', (done) => {
      try {
        delete apiKey.expireDate
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.EXPIRE_DATE} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount must be integer', (done) => {
      try {
        apiKey.maxCount = 10.5

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount should not be string', (done) => {
      try {
        apiKey.maxCount = '10.5'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('maxCount can not be null value', (done) => {
      try {
        apiKey.maxCount = null

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.MAX_COUNT} ${messages.m.INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('UserId is not found', (done) => {
      try {
        apiKey.UserId = 'e1c8e983-03ef-4d46-a3f1-8d6d1014031b'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FOREIGN_KEY_CONSTRAINT_ERROR}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('UserId is not uuid', (done) => {
      try {
        apiKey.UserId = 'kjh23j4h2jh34'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.USER_ID} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('incorrect field', (done) => {
      try {
        apiKey.field = 'kjshdfehjgashfjgashjf'

        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${token}`)
          .send(apiKey)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.FIELD_INCORRECT}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        const expiredToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'
        
        request
          .post('/api/apikeys')
          .set('Authorization', `Bearer ${expiredToken}`)
          .send(apiKey)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .post('/api/apikeys')
          .send(apiKey)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
